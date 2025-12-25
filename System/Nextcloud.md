# NEXTCLOUD

## Khởi tạo NextCloud
  - Clone máy Ubuntu (Xem lại CheckMK)
  - Cài NextCloud Compose trên Ubuntu
    - Cài Docker & Docker Compose
      - sudo apt update
      - sudo apt install ca-certificates curl gnupg lsb-release -y
      - sudo mkdir -p /etc/apt/keyrings
      - curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      - echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
      - sudo apt update
      - sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
      - Kiểm tra:
        - docker --version
        - docker compose version
      - Cho user chạy docker không cần sudo:
        - sudo usermod -aG docker $USER
        - newgrp docker
      - Start Docker:
        - systemctl start docker
      - Cho Docker tự chạy sau reboot: systemctl enable docker
      - Kiểm tra lại: systemctl status docker
  - Cài NextCloud
    - Tạo thư mục Nextcloud:
      - Tạo thư mục chuẩn để dễ backup:
        - mkdir -p ~/nextcloud
        - cd ~/nextcloud
    - Tạo file Docker Compose cho Nextcloud:
      - Tạo file: nano compose.yml
      - Paste nội dung sau: (tự đổi pass khác)
        ```
        version: "3.8"

        services:
          db:
            image: mariadb:10.11
            container_name: nextcloud_db
            restart: always
            volumes:
              - ./db:/var/lib/mysql
            environment:
              MYSQL_ROOT_PASSWORD: rootpassword
              MYSQL_DATABASE: nextcloud
              MYSQL_USER: nextcloud
              MYSQL_PASSWORD: nextcloudpassword
        
          app:
            image: nextcloud:latest
            container_name: nextcloud_app
            restart: always
            ports:
              - "8080:80"
            volumes:
              - ./nextcloud:/var/www/html
            environment:
              MYSQL_HOST: db
              MYSQL_DATABASE: nextcloud
              MYSQL_USER: nextcloud
              MYSQL_PASSWORD: nextcloudpassword
            depends_on:
              - db
        ```
    - Chạy Nextcloud:
      - docker compose up -d
      - Kiểm tra Container: docker ps
      - Vào Web GUI: http://172.16.20.35:8080/ (giao thức http; ip máy; port 8080 (có thể thay đổi trên file .yml)
      - Đăng nhập lần đầu:
       ![Ảnh 1](?raw=1)
      - Đổi pass admin
  - Back up:
    - Backup data: tar czvf nextcloud_backup.tar.gz nextcloud db compose.yml
    - Restore chỉ cần bung thư mục và chạy lại: docker compose up -d
 
    

---

## Khởi tạo KeyCloak
  - Tạo thư mục riêng cho Keycloak
    - mkdir -p /root/keycloak
    - cd /root/keycloak
  - Tạo file Docker Compose cho Keycloak:
    - nano compose.yml
    - Paste nội dung sau: (tự đổi pass khác)
    ```
    services:
    postgres:
      image: postgres:15
      container_name: keycloak_db
      restart: always
      volumes:
        - ./postgres:/var/lib/postgresql/data
      environment:
        POSTGRES_DB: keycloak
        POSTGRES_USER: keycloak
        POSTGRES_PASSWORD: keycloakpassword
    
    keycloak:
      image: quay.io/keycloak/keycloak:25.0.6
      container_name: keycloak
      restart: always
      command: start-dev
      ports:
        - "8081:8080"      //dùng port 8081 né 8080 của NextCloud
      environment:
        KC_DB: postgres
        KC_DB_URL_HOST: postgres
        KC_DB_USERNAME: keycloak
        KC_DB_PASSWORD: keycloakpassword
        KEYCLOAK_ADMIN: admin
        KEYCLOAK_ADMIN_PASSWORD: adminpassword
      depends_on:
        - postgres
    ```
  - Chạy KeyCloak
    - docker compose up -d
    - Kiểm tra: docker ps
    - Vào Web GUI: http://172.16.20.35:8081/ (giao thức http; ip máy; port 8080 (có thể thay đổi trên file .yml)
      - Đăng nhập lần đầu:
      ![Ảnh 2](?raw=1)
      - Đổi pass admin
      ![Ảnh 15](?raw=1)
      ![Ảnh 16](?raw=1)
      ![Ảnh 17](?raw=1)
  - Backup KeyCloak
    - backup database: tar czvf keycloak_backup.tar.gz postgres compose.yml
    - Restore: docker compose up -d

 ---
## Xác minh đăng nhập NextCloud bằng KeyCloak
  ### Luồng đăng nhập:
  Tổng quan luồng đăng nhập
   - Luồng chuẩn sẽ là:
     - User vào Nextcloud
     - Nextcloud chuyển user sang Keycloak
     - User đăng nhập ở Keycloak
     - Keycloak trả token về
     - Nextcloud xác thực và tạo user
   - Nextcloud đóng vai trò Client
   - Keycloak đóng vai trò Identity Provider

  ### Cấu hình KeyCloak
  - Tạo Realm mới
  ![Ảnh 3](?raw=1)
  ![Ảnh 4](?raw=1)
  - Tạo Client cho NextCloud
  ![Ảnh 5](?raw=1)
  ![Ảnh 6](?raw=1)
  ![Ảnh 7](?raw=1)
  ![Ảnh 8](?raw=1)
  - Lấy Client Secret
  ![Ảnh 9](?raw=1)
  - Tạo account
    - Tạo user 
    ![Ảnh 19](?raw=1)
    ![Ảnh 11](?raw=1)
    - Tạo password
    ![Ảnh 12](?raw=1)
    ![Ảnh 13](?raw=1)
    ![Ảnh 14](?raw=1)

  ### Cấu hình NextCloud
  - Cài app Social Login
  ![Ảnh 18](?raw=1)
  ![Ảnh 19](?raw=1)
  - Cấu hình OpenID Connect
  ![Ảnh 20](?raw=1)
    - Internal name: Keycloak
    - Title: KeyCloak
    - Authorize url: http://172.16.20.35:8081/realms/nextcloud/protocol/openid-connect/auth?prompt=login (mỗi lần đăng nhập bên KeyCloak được lựa chọn nhập pass hoặc đăng nhập mới)
    - Token url: http://172.16.20.35:8081/realms/nextcloud/protocol/openid-connect/token
    - Display name claim (optional): name
    - User info URL (optional): http://172.16.20.35:8081/realms/nextcloud/protocol/openid-connect/userinfo
    - Logout URL (optional):
    - Client Id: nextcloud
    - Client Secret: (Xem ở KeyCloak Client)
    - Scope: openid profile email
  - Cấu hình NextCloud nhận full name
    - Tạo Client Scope
    ![Ảnh 22](?raw=1)
    ![Ảnh 23](?raw=1)
    - Add Mapper
    ![Ảnh 24](?raw=1)
    ![Ảnh 25](?raw=1)
    ![Ảnh 26](?raw=1)
    - Add Scope vào Cloud
    ![Ảnh 27](?raw=1)
    ![Ảnh 28](?raw=1)
    ![Ảnh 29](?raw=1)
    - Check lại NextCloud đảm bảo chọn dòng: Display name claim (optional): name
    ![Ảnh 30](?raw=1)
  - Kiểm tra: Mở Web NextCloud
  ![Ảnh 21](?raw=1)
 
