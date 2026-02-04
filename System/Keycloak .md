# HƯỚNG DẪN TRIỂN KHAI KEYCLOAK VÀ TÍCH HỢP SSO VỚI NEXTCLOUD

---

## 1. Khởi tạo Keycloak bằng Docker Compose

### 1.1 Tạo thư mục triển khai Keycloak

```bash
mkdir -p /root/keycloak
cd /root/keycloak
```
### 1.2 Tạo file Docker Compose
```
nano compose.yml
```
- Nội dung file `compose.yml`: (Lưu ý thay đổi mật khẩu cho phù hợp môi trường thực tế)
```
services:
  keycloak_db:
    image: postgres:15
    container_name: keycloak_db
    restart: unless-stopped
    volumes:
      - ./postgres:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: keycloak
      POSTGRES_USER: keycloak
      POSTGRES_PASSWORD: keycloakpassword

  keycloak:
    image: quay.io/keycloak/keycloak:latest
    container_name: keycloak
    restart: unless-stopped
    command:
      - start
    ports:
      - "8081:8080"
    environment:
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: adminpassword

      KC_DB: postgres
      KC_DB_URL_HOST: keycloak_db
      KC_DB_URL_DATABASE: keycloak
      KC_DB_USERNAME: keycloak
      KC_DB_PASSWORD: keycloakpassword

      KC_HTTP_ENABLED: "true"

      KC_PROXY: edge
      KC_PROXY_HEADERS: xforwarded

      KC_HOSTNAME_URL: https://auth.cloudnvt.km0.vn
      KC_HOSTNAME_ADMIN_URL: https://auth.cloudnvt.km0.vn
      KC_HOSTNAME_STRICT: "true"

    depends_on:
      - keycloak_db
```
### 1.3 Khởi động Keycloak
```
docker compose up -d
```
### 1.4 Kiểm tra container
- Phải thấy:
  - `keycloak`
  - `keycloak_db `
 
### 1.5 Truy cập giao diện quản trị Keycloak
```
https://auth.cloudnvt.km0.vn/admin
```
- Đăng nhập bằng tài khoản admin đã khai báo.
![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/2.png?raw=1)
- Đổi mật khẩu admin sau lần đăng nhập đầu tiên.
![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/15.png?raw=1)
![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/16.png?raw=1)
![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/17.png?raw=1)

## 2. Tổng quan luồng đăng nhập SSO Nextcloud với Keycloak
### 2.1 Luồng xác thực chuẩn
- User truy cập Nextcloud
- Nextcloud chuyển hướng sang Keycloak
- User đăng nhập tại Keycloak
- Keycloak trả token OpenID Connect
- Nextcloud xác thực và tạo user tương ứng

- Trong mô hình này
  - Nextcloud đóng vai trò Client
  - Keycloak đóng vai trò Identity Provider
## 3. Cấu hình Keycloak cho Nextcloud
### 3.1 Tạo Realm mới
![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/3.png?raw=1)
![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/4.png?raw=1)
### 3.2 Tạo Client cho Nextcloud
![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/5.png?raw=1)
![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/6.png?raw=1)
![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/7.png?raw=1)
![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/8.png?raw=1)
### 3.3 Lấy Client Secret
![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/9.png?raw=1)
### 3.4 Tạo user và mật khẩu
![Ảnh 19](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/19.png?raw=1)
![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/11.png?raw=1)
![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/12.png?raw=1)
![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/13.png?raw=1)
![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/14.png?raw=1)

## 4. Cấu hình Nextcloud đăng nhập bằng Keycloak
### 4.1 Cài app Social Login
![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/18.png?raw=1)
### 4.2 Cấu hình OpenID Connect
- Các thông số cấu hình:
  - Internal name: `Keycloak `
  - Title: `Keycloak`
  - Authorize URL: `https://auth.cloudnvt.km0.vn/realms/nextcloud/protocol/openid-connect/auth?prompt=login`
  - Token URL: `https://auth.cloudnvt.km0.vn/realms/nextcloud/protocol/openid-connect/token`
  - User info URL: `https://auth.cloudnvt.km0.vn/realms/nextcloud/protocol/openid-connect/userinfo`
  - Client ID: `nextcloud`
  - Client Secret: Lấy từ Keycloak Client
  - Scope: `openid profile email`
  - Display name claim: `name`
![Ảnh 20](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/20.png?raw=1)

## 5. Cấu hình hiển thị Full Name cho user
### 5.1 Tạo Client Scope và Mapper
![Ảnh 22](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/22.png?raw=1)
![Ảnh 23](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/23.png?raw=1)
![Ảnh 24](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/24.png?raw=1)
![Ảnh 25](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/25.png?raw=1)
![Ảnh 26](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/26.png?raw=1)
### 5.2 Gán Scope vào Client
![Ảnh 27](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/27.png?raw=1)
![Ảnh 28](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/28.png?raw=1)
![Ảnh 29](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/29.png?raw=1)
### 5.3 Kiểm tra lại Display name claim
![Ảnh 30](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/30.png?raw=1)

## 6. Kiểm tra đăng nhập SSO
- Mở trang Nextcloud và chọn đăng nhập bằng Keycloak
![Ảnh 21](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/21.png?raw=1)



