# TỰ ĐỘNG ĐỔI MÃ HASH TRONG ATTRIBUTE MAILCOW_PASSWORD TRÊN KEYCLOAK KHI NGƯỜI DÙNG ĐỔI PASS

## 1. Tạo API sinh Hash trên server Mailcow
### 1.1 Nguyên tắc bảo mật (bắt buộc)
- API chỉ bind private IP hoặc localhost
- Chỉ Keycloak server gọi được
- Không log plaintext
- Không lưu plaintext
- Có shared secret hoặc mTLS
- Đây là API nội bộ, không phải public service.

### 1.2 Tạo API đơn giản bằng Python Flask
- Cài môi trường trên server Mailcow
```
apt update
apt install -y python3 python3-pip
pip3 install flask
```
- Tạo file API
```
mkdir -p /opt/mailcow-hash-api
nano /opt/mailcow-hash-api/app.py
```
- Nội dung `app.py`
```
from flask import Flask, request, jsonify, abort
import subprocess
import os

app = Flask(__name__)

SHARED_SECRET = "CHANGE_ME_SECRET"

@app.route("/hash", methods=["POST"])
def hash_password():
    if request.headers.get("X-Auth-Token") != SHARED_SECRET:
        abort(401)

    data = request.get_json()
    if not data or "password" not in data:
        abort(400)

    password = data["password"]

    proc = subprocess.Popen(
        [
            "docker", "exec", "-i",
            "mailcowdockerized-dovecot-mailcow-1",
            "doveadm", "pw", "-s", "BLF-CRYPT"
        ],
        stdin=subprocess.PIPE,
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        text=True
    )

    out, err = proc.communicate(password + "\n" + password + "\n")

    if proc.returncode != 0:
        abort(500)

    hash_line = out.strip().splitlines()[-1]
    if not hash_line.startswith("{BLF-CRYPT}"):
        abort(500)

    return jsonify({"hash": hash_line})
```
- Chạy API (chỉ bind private) ; K đc bấm `Ctrl + C`
```
cd /opt/mailcow-hash-api
FLASK_RUN_HOST=127.0.0.1 FLASK_RUN_PORT=18080 flask run
```
- Mở 1 SSH khác test API:
  - Kiểm tra Port `18080` có chạy không:
  ```
  ss -lntp | grep 18080
  ```
  - Test API sinh Hash
  ```
  curl -X POST http://127.0.0.1:18080/hash \
    -H "Content-Type: application/json" \
    -H "X-Auth-Token: CHANGE_ME_SECRET" \
    -d '{"password":"abcabc"}'
  ```

## 2. Nghe sự kiện đổi Password trong Keycloak (Required Action - LỖI KHÔNG CHẠY ĐC)
### 2.1 Tạo Required Action Provider
- Flow trong Required Action:
    - User đổi password
    - Keycloak nhận password mới
    - Before commit
    - Call API Mailcow `/hash`
    - Nhận `hash`
    - Set: `user.setSingleAttribute("mailcow_password", hash)`
    - Cho phép đổi password hoàn tất

### 2.2 Pseudo-code Required Action

#### Build plugin Required Action
- Cài Java và Maven trên VM Keycloak dùng để build jar
```
apt update
apt install -y openjdk-17-jdk maven
java -version
mvn -version
```
- Tạo thư mục project
```
mkdir -p /root/kc-mailcow-sync
cd /root/kc-mailcow-sync
```
- Tạo file pom.xml
```
nano pom.xml
```
- Nội dung file pom.yml
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>vn.cloudnvt</groupId>
  <artifactId>kc-mailcow-sync</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>

  <properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <keycloak.version>26.5.2</keycloak.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.keycloak</groupId>
      <artifactId>keycloak-server-spi</artifactId>
      <version>${keycloak.version}</version>
    </dependency>
    <dependency>
      <groupId>org.keycloak</groupId>
      <artifactId>keycloak-server-spi-private</artifactId>
      <version>${keycloak.version}</version>
    </dependency>
    <dependency>
      <groupId>org.keycloak</groupId>
      <artifactId>keycloak-services</artifactId>
      <version>${keycloak.version}</version>
    </dependency>
  </dependencies>
</project>

```
- Tạo cấu trúc source
```
mkdir -p src/main/java/vn/cloudnvt/keycloak/mailcow
mkdir -p src/main/resources/META-INF/services
```
- Tạo file Required Action code
```
nano src/main/java/vn/cloudnvt/keycloak/mailcow/MailcowPasswordRequiredAction.java
```
- Nội dung file:
```
package vn.cloudnvt.keycloak.mailcow;

import jakarta.ws.rs.core.MultivaluedMap;
import org.keycloak.authentication.RequiredActionContext;
import org.keycloak.authentication.RequiredActionProvider;
import org.keycloak.models.UserModel;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class MailcowPasswordRequiredAction implements RequiredActionProvider {

    private static final String API_URL = "http://172.16.20.73:18080/hash";
    private static final String API_TOKEN = "CHANGE_ME_SECRET";

    @Override
    public void evaluateTriggers(RequiredActionContext context) {
        // Không trigger tự động
    }

    @Override
    public void requiredActionChallenge(RequiredActionContext context) {
        // KHÔNG render form
        // Keycloak đã render form UPDATE_PASSWORD chuẩn
    }

    @Override
    public void processAction(RequiredActionContext context) {

        MultivaluedMap<String, String> form =
                context.getHttpRequest().getDecodedFormParameters();

        String newPassword = form.getFirst("password-new");

        if (newPassword == null || newPassword.isEmpty()) {
            context.failure();
            return;
        }

        try {
            String json = "{\"password\":\"" + escapeJson(newPassword) + "\"}";

            HttpRequest request = HttpRequest.newBuilder()
                    .uri(URI.create(API_URL))
                    .header("Content-Type", "application/json")
                    .header("X-Auth-Token", API_TOKEN)
                    .POST(HttpRequest.BodyPublishers.ofString(json))
                    .build();

            HttpClient client = HttpClient.newHttpClient();
            HttpResponse<String> response =
                    client.send(request, HttpResponse.BodyHandlers.ofString());

            if (response.statusCode() != 200) {
                context.failure();
                return;
            }

            String hash = extractHash(response.body());
            if (hash == null || !hash.startsWith("{BLF-CRYPT}")) {
                context.failure();
                return;
            }

            UserModel user = context.getUser();
            user.setSingleAttribute("mailcow_password", hash);

            context.success();

        } catch (Exception e) {
            context.failure();
        }
    }

    private static String extractHash(String body) {
        int i = body.indexOf("\"hash\"");
        if (i < 0) return null;
        int c = body.indexOf(":", i);
        if (c < 0) return null;
        int q1 = body.indexOf("\"", c);
        if (q1 < 0) return null;
        int q2 = body.indexOf("\"", q1 + 1);
        if (q2 < 0) return null;
        return body.substring(q1 + 1, q2);
    }

    private static String escapeJson(String s) {
        return s.replace("\\", "\\\\").replace("\"", "\\\"");
    }

    @Override
    public void close() {}
}
```
- Tạo file Factory
```
nano src/main/java/vn/cloudnvt/keycloak/mailcow/MailcowPasswordRequiredActionFactory.java
```
- Nội dung: 
```
package vn.cloudnvt.keycloak.mailcow;

import org.keycloak.Config;
import org.keycloak.authentication.RequiredActionFactory;
import org.keycloak.authentication.RequiredActionProvider;
import org.keycloak.models.KeycloakSession;
import org.keycloak.models.KeycloakSessionFactory;

public class MailcowPasswordRequiredActionFactory implements RequiredActionFactory {

    public static final String ID = "mailcow-password-sync";

    @Override
    public RequiredActionProvider create(KeycloakSession session) {
        return new MailcowPasswordRequiredAction();
    }

    @Override
    public String getId() {
        return ID;
    }

    @Override
    public String getDisplayText() {
        return "Sync Mailcow password hash";
    }

    @Override
    public void init(Config.Scope config) {}

    @Override
    public void postInit(KeycloakSessionFactory factory) {}

    @Override
    public void close() {}
}
```
- Đăng ký provider để Keycloak load
```
nano src/main/resources/META-INF/services/org.keycloak.authentication.RequiredActionFactory
```
- Dán dòng này vào:
```
vn.cloudnvt.keycloak.mailcow.MailcowPasswordRequiredActionFactory
```
- Build jar
```
cd /root/kc-mailcow-sync
mvn clean package
ls -lh target/
```
- Phải thấy file: `target/kc-mailcow-sync-1.0.0.jar`

#### Copy jar vào Keycloak và chạy
- Mount thư mục providers từ host vào container
  - Mở file
  ```
  nano /root/keycloak/compose.yml
  ```
  - Sửa service keycloak: thêm phần:
  ```
      volumes:
      - ./providers:/opt/keycloak/providers
  ```
  ![Ảnh 1](?raw=1)
- Tạo thư mục providers trên host
```
mkdir -p /root/keycloak/providers
```
- Copy JAR vào host
```
cp /root/kc-mailcow-sync/target/kc-mailcow-sync-1.0.0.jar \
   /root/keycloak/providers/
```
- Restart Keycloak
```
cd /root/keycloak
docker compose down
docker compose up -d
```
- Kiểm tra
  - Kiểm tra Log:
  ```
  docker logs keycloak | grep mailcow
  ```
  Thấy: `mailcow-password-sync (vn.cloudnvt.keycloak.mailcow.MailcowPasswordRequiredActionFactory)`
  - Vào Web GUI Keycloak:
  ![Ảnh 2](?raw=1)
  ![Ảnh 3](?raw=1)
- 

## 3. Làm Event Listener ( MỚI - THAY CHO ##2)
### Tạo Project Maven cho Keycloak Provider (trên VM Keycloak)
- Tạo thư mục
```
mkdir -p /root/kc-mailcow-event-listener
cd /root/kc-mailcow-event-listener
```
- Tạo pom.xml
```
nano pom.xml
```
- Nội dung: 
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <groupId>vn.cloudnvt</groupId>
  <artifactId>kc-mailcow-event-listener</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>

  <properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <keycloak.version>26.5.2</keycloak.version>
  </properties>

  <dependencies>

    <!-- Keycloak SPI -->
    <dependency>
      <groupId>org.keycloak</groupId>
      <artifactId>keycloak-server-spi</artifactId>
      <version>${keycloak.version}</version>
      <scope>provided</scope>
    </dependency>

    <!-- BẮT BUỘC: chứa org.keycloak.Config -->
    <dependency>
      <groupId>org.keycloak</groupId>
      <artifactId>keycloak-server-spi-private</artifactId>
      <version>${keycloak.version}</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>org.keycloak</groupId>
      <artifactId>keycloak-services</artifactId>
      <version>${keycloak.version}</version>
      <scope>provided</scope>
    </dependency>

  </dependencies>

</project>
```
- Lưu và thoát.

### Viết Event Listener
- Tạo thư mục
```
mkdir -p /root/kc-mailcow-event-listener
cd /root/kc-mailcow-event-listener
```
- Tạo pom.xml
```
nano pom.xml
```
- Nội dung:
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>vn.cloudnvt</groupId>
    <artifactId>kc-mailcow-event-listener</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <properties>
        <keycloak.version>26.5.2</keycloak.version>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-server-spi</artifactId>
            <version>${keycloak.version}</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-server-spi-private</artifactId>
            <version>${keycloak.version}</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-services</artifactId>
            <version>${keycloak.version}</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
</project>
```
- Tạo package + class
```
mkdir -p src/main/java/vn/cloudnvt/keycloak/event
```
- Tạo `MailcowPasswordEventListener.java`
```
nano MailcowPasswordEventListener.java
```
- Nội dung:
```
package vn.cloudnvt.keycloak.event;

import org.keycloak.events.Event;
import org.keycloak.events.EventType;
import org.keycloak.events.EventListenerProvider;
import org.keycloak.models.KeycloakSession;
import org.keycloak.models.RealmModel;
import org.keycloak.models.UserModel;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class MailcowPasswordEventListener implements EventListenerProvider {

    private final KeycloakSession session;

    public MailcowPasswordEventListener(KeycloakSession session) {
        this.session = session;
    }

    @Override
    public void onEvent(Event event) {
        if (event.getType() != EventType.UPDATE_PASSWORD) {
            return;
        }

        RealmModel realm = session.realms().getRealm(event.getRealmId());
        UserModel user = session.users().getUserById(realm, event.getUserId());

        if (user == null) return;

        String username = user.getUsername();
        System.out.println("[MAILCOW-EVENT] User updated password: " + username);

        try {
            String json = "{\"username\":\"" + username + "\"}";

            HttpRequest request = HttpRequest.newBuilder()
                    .uri(URI.create("http://mailcow-hash-api:18080/hash"))
                    .header("Content-Type", "application/json")
                    .header("X-Auth-Token", "CHANGE_ME_SECRET")
                    .POST(HttpRequest.BodyPublishers.ofString(json))
                    .build();

            HttpClient client = HttpClient.newHttpClient();
            HttpResponse<String> response =
                    client.send(request, HttpResponse.BodyHandlers.ofString());

            if (response.statusCode() == 200) {
                String hash = response.body();
                user.setSingleAttribute("mailcow_password", hash);
                System.out.println("[MAILCOW-EVENT] mailcow_password updated");
            } else {
                System.err.println("[MAILCOW-EVENT] API error " + response.statusCode());
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    public void close() {
    }
}
```
- Tạo `MailcowPasswordEventListenerFactory.java`
```
nano MailcowPasswordEventListenerFactory.java
```
- Nội dung:
```
package vn.cloudnvt.keycloak.event;

import org.keycloak.Config;
import org.keycloak.events.EventListenerProvider;
import org.keycloak.events.EventListenerProviderFactory;
import org.keycloak.models.KeycloakSession;
import org.keycloak.models.KeycloakSessionFactory;

public class MailcowPasswordEventListenerFactory
        implements EventListenerProviderFactory {

    @Override
    public EventListenerProvider create(KeycloakSession session) {
        return new MailcowPasswordEventListener(session);
    }

    @Override
    public void init(Config.Scope config) {
    }

    @Override
    public void postInit(KeycloakSessionFactory factory) {
    }

    @Override
    public void close() {
    }

    @Override
    public String getId() {
        return "mailcow-password-event";
    }
}
```
- 
### Đăng kí SIP
- Tạo file service loader
```
mkdir -p src/main/resources/META-INF/services
```
- Tạo file SPI
```
nano src/main/resources/META-INF/services/org.keycloak.events.EventListenerProviderFactory
```
- Nội dung
```
vn.cloudnvt.keycloak.event.MailcowPasswordEventListenerFactory
```
- BUILD JAR
```
mvn clean package
```
- Sau khi xong sẽ có file: `target/kc-mailcow-event-listener-1.0.0.jar`

### CÀI VÀO KEYCLOAK
- Copy JAR vào Keycloak
```
mkdir -p /root/keycloak/providers
cp /root/kc-mailcow-event-listener/target/kc-mailcow-event-listener-1.0.0.jar \
   /root/keycloak/providers/
```
- Kiểm tra:
```
ls -lh /root/keycloak/providers
```
- thấy file `.jar`
- Mount providers vào container Keycloak:
  - Mở file docker compose của Keycloak: 
  ```
  nano /root/keycloak/compose.yml
  ```
  ![Ảnh 1](?raw=1)
- Rebuild Keycloak
```
cd /root/keycloak
docker compose down
docker compose up -d
```
- Vào container build lại:
```
docker exec -it keycloak /opt/keycloak/bin/kc.sh build
docker restart keycloak
```
- Xác nhận Keycloak đã load Event Listener
  - Kiểm tra
  ```
  docker logs keycloak | grep mailcow
  ```
  - Thấy ` mailcow-password-event-listener ...`
- 
