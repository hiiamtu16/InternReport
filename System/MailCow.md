# MAILCOW

## Cài đặt MailCow trên máy Ubuntu
  - CÀI DOCKER + DEPENDENCIES CHO MAILCOW
    - Update hệ thống:
      - apt update
      - apt update -y
    - Cài JQ: apt install -y jq
    - Cài Docker:
      ```
      apt install -y ca-certificates curl gnupg lsb-release
      mkdir -p /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
      https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
      > /etc/apt/sources.list.d/docker.list
      apt update
      apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
      ```
    - Enable Docker:
     ```
     systemctl enable docker
     systemctl start docker
     docker --version
     docker compose version
     ```
  - CLONE MAILCOW:
    - cd /opt
    - git clone https://github.com/mailcow/mailcow-dockerized
    - cd mailcow-dockerized
  - KHỞI TẠO CẤU HÌNH MAILCOW:
    - ./generate_config.sh
    - Script sẽ hỏi: Mail server hostname (FQDN):
    - Điền: mail.cloudnvt.km0.vn
  - CHỈNH CẤU HÌNH TỐI THIỂU
    - Mở file cấu hình: nano mailcow.conf
    - Thêm tạm: SKIP_LETS_ENCRYPT=y (tạm thời bỏ qua DNS)
    - Lưu file rồi chạy Mailcow lần đầu
      - docker compose pull
      - docker compose up -d
 ---
 ## Cấu hình DNS:
   - Tạo thêm bản ghi cho MailCow (dựa trên bản ghi đã có của NextCloud và KeyCloak)
    ![Ảnh 1](?raw=1)
   - Tạo NAT và Rule cho MailCow
     -  Tạo VIPs và mở Port dịch vụ
     ![Ảnh 2](?raw=1)
     -  Add VIPs vào Rule
     ![Ảnh 3](?raw=1)
     -  Kiểm tra: nc -vz mail.cloudnvt.km0.vn 25 (thấy success / có thể test các port khác)

---
## Cấu hình MailCow
  - Đăng nhập trang quản trị

  - Add domain

  - Tạo MailBox User để test dịch vụ

  - Test gửi và nhận mail

  - Tích hợp KeyCloak
    - Trên KeyCloak:
      - Tạo thêm Client cho MailCow
     
      - Copy Secret
    - Trên MailCow:

---
## HTTPS cho MailCow bằng CertBot (LƯU Ý: XIN CERT THỦ CÔNG, PHẢI GIA HẠN CERT THỦ CÔNG SAU 90 NGÀY)
  - Cài Cert Bot:
    - apt update
    - apt install -y certbot
    - Kiểm tra: certbot --version
  - Kiểm tra Cert: certbot certificates
  - Xin Cert:
  ```
  certbot certonly \
    --manual \
    --preferred-challenges dns \
    -d mail2.cloudnvt.km0.vn
  ```
  
  ![Ảnh 15](?raw=1)
  - Tạo bản ghi TXT trên DNS giống với CertBot gửi thông tin
  ![Ảnh 16](?raw=1)
  - Kiểm tra bản ghi TXT trên 1 PuTTY khác (giống, ok ==> quay lại PuTTY xin cert Enter): dig TXT _acme-challenge.mail2.cloudnvt.km0.vn +short
  ![Ảnh 17](?raw=1)
  - Kiểm tra Cert: certbot certificates
  ![Ảnh 18](?raw=1)
  - Xác nhận cert tồn tại: ls -l /etc/letsencrypt/live/mail.cloudnvt.km0.vn/

---
## Đồng bộ user từ KeyCloak sang MailCow Server (sử dụng API)
  - Tạo API cho MailCow trên MailCow Server:
    - Di chuyển đến thư mục: cd /opt/mailcow-dockerized 
    - Tạo API_KEY:  openssl rand -hex 32
    ![Ảnh 19](?raw=1)
    - Mở file cấu hình: nano mailcow.conf
    - Bấm Ctrl+W để tìm kiếm, tìm từ khoá "API_KEY="
    - Điền API_KEY vừa tạo, cho phép trên các IP
    ![Ảnh 20](?raw=1)
    - Lưu lại và reload docker
      - docker compose down
      - docker compose up -d
    - Test API: Test lấy danh sách mailbox: curl -k https://mail.cloudnvt.km0.vn/api/v1/get/mailbox/all \-H "X-API-Key: **API_KEY_vừa_tạo**"
  - Tạo thư mục và môi trường chạy script:
    ```
    sudo mkdir -p /opt/keycloak_mailcow_sync
    cd /opt/keycloak_mailcow_sync
    
    sudo apt update
    sudo apt install -y python3 python3-venv python3-pip jq
    
    python3 -m venv venv
    source venv/bin/activate
    pip install --upgrade pip
    pip install requests
    ```
  - Tạo file cấu hình dạng env: nano /opt/keycloak_mailcow_sync/.env
  - Paste vào file:
  ```
  MAILCOW_BASE_URL=https://mail.cloudnvt.km0.vn
  MAILCOW_API_KEY=2140017e3169dc582fce42b8db47c29e89fb8e68e5e583530a65221a0258d926
  MAILCOW_MAIL_DOMAIN=cloudnvt.km0.vn
  
  KEYCLOAK_BASE_URL=https://<keycloak-domain>
  KEYCLOAK_REALM=<realm-name>
  KEYCLOAK_CLIENT_ID=<client-id>
  KEYCLOAK_CLIENT_SECRET=<client-secret>
  
  SYNC_ONLY_DOMAIN_USERS=1
  DRY_RUN=0
  ```
  - Tạo script sync: nano /opt/keycloak_mailcow_sync/sync.py
  - Paste vào file:
  ```
    import os
    import sys
    import json
    import secrets
    import string
    import requests
    from urllib.parse import urljoin
    
    def load_env(path: str) -> None:
        if not os.path.exists(path):
            return
        with open(path, "r", encoding="utf-8") as f:
            for line in f:
                line = line.strip()
                if not line or line.startswith("#"):
                    continue
                if "=" not in line:
                    continue
                k, v = line.split("=", 1)
                os.environ.setdefault(k.strip(), v.strip())
    
    def must_get(name: str) -> str:
        v = os.getenv(name, "").strip()
        if not v:
            print(f"Missing env: {name}", file=sys.stderr)
            sys.exit(2)
        return v
    
    def rand_password(length: int = 20) -> str:
        alphabet = string.ascii_letters + string.digits
        return "".join(secrets.choice(alphabet) for _ in range(length))
    
    def keycloak_get_token(base_url: str, realm: str, client_id: str, client_secret: str) -> str:
        token_url = urljoin(base_url.rstrip("/") + "/", f"realms/{realm}/protocol/openid-connect/token")
        resp = requests.post(
            token_url,
            data={
                "grant_type": "client_credentials",
                "client_id": client_id,
                "client_secret": client_secret,
            },
            timeout=30,
        )
        resp.raise_for_status()
        return resp.json()["access_token"]
    
    def keycloak_list_users(base_url: str, realm: str, token: str, page_size: int = 2000):
        users = []
        first = 0
        while True:
            url = urljoin(base_url.rstrip("/") + "/", f"admin/realms/{realm}/users")
            resp = requests.get(
                url,
                headers={"Authorization": f"Bearer {token}"},
                params={"first": first, "max": page_size},
                timeout=60,
            )
            resp.raise_for_status()
            batch = resp.json()
            if not batch:
                break
            users.extend(batch)
            first += page_size
        return users
    
    def mailcow_api_get_all_mailboxes(base_url: str, api_key: str):
        url = urljoin(base_url.rstrip("/") + "/", "api/v1/get/mailbox/all")
        resp = requests.get(url, headers={"X-API-Key": api_key}, timeout=60, verify=False)
        resp.raise_for_status()
        return resp.json()
    
    def mailcow_api_add_mailbox(base_url: str, api_key: str, payload: dict):
        url = urljoin(base_url.rstrip("/") + "/", "api/v1/add/mailbox")
        resp = requests.post(
            url,
            headers={"X-API-Key": api_key, "Content-Type": "application/json"},
            data=json.dumps([payload]),
            timeout=60,
            verify=False,
        )
        resp.raise_for_status()
        return resp.json()
    
    def main():
        load_env("/opt/keycloak_mailcow_sync/.env")
    
        mailcow_base = must_get("MAILCOW_BASE_URL")
        mailcow_key = must_get("MAILCOW_API_KEY")
        mail_domain = must_get("MAILCOW_MAIL_DOMAIN")
    
        kc_base = must_get("KEYCLOAK_BASE_URL")
        kc_realm = must_get("KEYCLOAK_REALM")
        kc_client_id = must_get("KEYCLOAK_CLIENT_ID")
        kc_client_secret = must_get("KEYCLOAK_CLIENT_SECRET")
    
        sync_only_domain = os.getenv("SYNC_ONLY_DOMAIN_USERS", "1").strip() == "1"
        dry_run = os.getenv("DRY_RUN", "0").strip() == "1"
    
        print("Step 1 Get Keycloak token")
        token = keycloak_get_token(kc_base, kc_realm, kc_client_id, kc_client_secret)
    
        print("Step 2 List Keycloak users")
        kc_users = keycloak_list_users(kc_base, kc_realm, token)
        print(f"Keycloak users fetched: {len(kc_users)}")
    
        print("Step 3 Get Mailcow mailboxes")
        mailcow_boxes = mailcow_api_get_all_mailboxes(mailcow_base, mailcow_key)
        existing = set()
        for mb in mailcow_boxes:
            u = mb.get("username", "").strip().lower()
            if u:
                existing.add(u)
        print(f"Mailcow mailboxes existing: {len(existing)}")
    
        created = 0
        skipped = 0
    
        print("Step 4 Sync")
        for u in kc_users:
            if not u.get("enabled", True):
                continue
    
            email = (u.get("email") or "").strip().lower()
            if not email or "@" not in email:
                continue
    
            if sync_only_domain and not email.endswith("@" + mail_domain):
                continue
    
            if email in existing:
                skipped += 1
                continue
    
            local_part = email.split("@", 1)[0]
            display_name = (u.get("firstName") or "").strip()
            if u.get("lastName"):
                display_name = (display_name + " " + u["lastName"].strip()).strip()
            if not display_name:
                display_name = local_part
    
            pw = rand_password()
    
            payload = {
                "local_part": local_part,
                "domain": mail_domain,
                "name": display_name,
                "password": pw,
                "password2": pw,
                "quota": "0",
                "active": "1",
            }
    
            print(f"Create mailbox: {email}")
            if dry_run:
                print("DRY_RUN enabled, not creating")
                created += 1
                continue
    
            try:
                res = mailcow_api_add_mailbox(mailcow_base, mailcow_key, payload)
                print(res)
                created += 1
            except Exception as e:
                print(f"Create failed for {email}: {e}", file=sys.stderr)
    
        print("Done")
        print(f"Created: {created}")
        print(f"Skipped existing: {skipped}")
    
    if __name__ == "__main__":
        main()
  ```
- Chạy thử và chạy thật:
    - Chạy thử không tạo user:
      ```
      cd /opt/keycloak_mailcow_sync
      source venv/bin/activate
      sed -i 's/^DRY_RUN=.*/DRY_RUN=1/' .env
      python3 sync.py
      ```
    - Chạy thật: 
      ```
      sed -i 's/^DRY_RUN=.*/DRY_RUN=0/' .env
      python3 sync.py
      ```



---
 ## HTTPS cho MailCow trên HAProxy (HTTP nội bộ sau HAProxy, HTTPS bên ngoài)
  - Chỉnh MailCow HTTP nội bộ:
    - Mở file cấu hình MailCow: nano /opt/mailcow-dockerized/mailcow.conf
    - Kiểm tra:
      - MAILCOW_HOSTNAME=mail.cloudnvt.km0.vn
      - SKIP_LETS_ENCRYPT=y
    - Lưu file:
    - Restart MailCow:
      - cd /opt/mailcow-dockerized
      - docker compose down
      - docker compose up -d
  - Thêm Domain MailCow vào HAProxy
    - Trên máy HAProxy: Chỉ mở rộng https_in
      - acl host_mailcow hdr(host) -i mail.cloudnvt.km0.vn (acl)
      - use_backend mailcow_backend if host_mailcow  (rule)
  - Tạo BackEnd MailCow:
    - Thêm vào cuối file haproxy.cfg:
      - backend mailcow_backend
      - mode http
      - server mailcow 172.16.20.38:80 check
  - Xin HTTPS cho MailCow:
    - Xin Cert: certbot certonly --standalone -d mail.cloudnvt.km0.vn
    - Sau khi có Cert:
     ```
      cat /etc/letsencrypt/live/mail.cloudnvt.km0.vn/fullchain.pem \
        /etc/letsencrypt/live/mail.cloudnvt.km0.vn/privkey.pem \
        > /etc/haproxy/certs/mail.cloudnvt.km0.vn.pem
     
      chmod 600 /etc/haproxy/certs/mail.cloudnvt.km0.vn.pem
      chown root:root /etc/haproxy/certs/mail.cloudnvt.km0.vn.pem
     ```
    - Thêm Cert vào Bind :
      - trên máy HAProxy: sudo nano /etc/haproxy/haproxy.cfg
      - Sửa dòng bind *:443 ssl thành: bind *:443 ssl crt /etc/haproxy/certs/cloudnvt.km0.vn.pem crt /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem crt /etc/haproxy/certs/mail.cloudnvt.km0.vn.pem
  - Reload và kiểm tra:
    - Reload:
      - haproxy -c -f /etc/haproxy/haproxy.cfg
      - systemctl reload haproxy
    - Test:
      - curl -I https://mail.cloudnvt.km0.vn
      - Kết quả: HTTP 302 hoặc 200
  - Sửa cấu hình MailCow:
    - Mở file cấu hình trên máy MailCow: nano /opt/mailcow-dockerized/mailcow.conf
    - Kiểm tra đúng các dòng:
      - MAILCOW_HOSTNAME=mail.cloudnvt.km0.vn
      - HTTP_PORT=80
      - HTTPS_PORT=443
      - ENABLE_SSL=n
      - SKIP_LETS_ENCRYPT=y
    - Thêm các dòng (nếu chưa có; có rồi thì sửa):
      - ENABLE_SSL=n
      - HTTP_REDIRECT=n
      - SKIP_LETS_ENCRYPT=y
      - TRUSTED_PROXIES=172.16.20.36
      - FORCE_HTTPS=n
    - Save và Reload MailCow:
      -  cd /opt/mailcow-dockerized
      -  docker compose down
      -  docker compose pull
      -  docker compose up -d

---
## Gửi Mail qua nền tảng Google Mail
  - Sử dụng Gmail Relay:
    - Vào trang: https://myaccount.google.com/
    - Tìm app password tạo mk cho mailcow   
    - cop pass sinh ra: bwwq irug nyuy ndev 
