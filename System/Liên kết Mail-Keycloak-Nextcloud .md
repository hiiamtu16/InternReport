# HƯỚNG DẪN XÁC MINH ĐĂNG NHẬP MAILCOW QUA KEYCLOAK, ADD MAIL SERVER VÀO NEXTCLOUD

## Cấu hình trên Keycloak:
- Tạo Client `mailcow`
  ![Ảnh 1](?raw=1)
  ![Ảnh 2](?raw=1)
  ![Ảnh 3](?raw=1)
- Bật `Service Account Role`:
  ![Ảnh 4](?raw=1)
  ![Ảnh 5](?raw=1)
- Tạo `user_attribute`:
  - BƯỚC 1: Tạo Client Scope cho Mailcow Password
  ![Ảnh 6](?raw=1)
  ![Ảnh 7](?raw=1)
  - BƯỚC 2: Tạo Mapper cho password
  ![Ảnh 8](?raw=1)
  ![Ảnh 9](?raw=1)
  ![Ảnh 10](?raw=1)
  - BƯỚC 3: Gán Client Scope vào Client `mailcow`
  ![Ảnh 11](?raw=1)
  - BƯỚC 4: Tạo attribute cho USER
  ![Ảnh 12](?raw=1)
  ![Ảnh 13](?raw=1)
  - BƯỚC 5: Gán attribute cho USER
    - Tạo mã hash trên server Mailcow:
      - Tìm Container `dovecot`:
        ```
        docker ps --format "table {{.Names}}\t{{.Image}}"
        ```
        ![Ảnh 14](?raw=1)
      - Chạy lệnh lấy mã hash:
        ```
        docker exec -it mailcowdockerized-dovecot-mailcow-1 \
        doveadm pw -s BLF-CRYPT
        ```
      - Nhập password của user vào để lấy mã hash
        ![Ảnh 15](?raw=1)
        ![Ảnh 16](?raw=1)

## Cấu hình trên MailCow
- Kiểm tra Domain Mail
![Ảnh 17](?raw=1)
- Kết nối Mailcow với Keycloak
  - Lấy Secret client:
   ![Ảnh 18](?raw=1)
  - Cấu hình connect Mailcow - Keycloak
   ![Ảnh 19](?raw=1)
- Sync lại user:
  ```
  cd /opt/mailcow-dockerized
  docker compose restart
  ```
- Kiểm tra login
   ![Ảnh 20](?raw=1)
   ![Ảnh 21](?raw=1)

## Cấu hình Mail trên Nextcloud
   ![Ảnh 22](?raw=1)
   ![Ảnh 23](?raw=1)
