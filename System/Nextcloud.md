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

 ---

 ## HA Proxy
   - Cài HA Proxy:
     - Lệnh cài:
       - sudo apt update
       - sudo apt install -y haproxy
     - Kiểm tra: haproxy -v
   - Tắt HAProxy mặc định (chưa cấu hình): sudo systemctl stop haproxy
   - Cấu hình HAProxy
     - Backup config: sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
     -  Mở file cấu hình: sudo nano /etc/haproxy/haproxy.cfg
     -  Xoá và paste đoạn này:
      ```
        global
            log /dev/log local0
            maxconn 4096
            daemon
        
        defaults
            log global
            mode http
            option httplog
            timeout connect 5s
            timeout client  60s
            timeout server  60s
        
        frontend http_in
            bind *:80
            mode http
            default_backend nextcloud_backend
        
        backend nextcloud_backend
            mode http
            server nextcloud 127.0.0.1:8080 check
      ```
     - Start HAProxy: 
       - sudo systemctl start haproxy
       - sudo systemctl status haproxy
   - Test HAProxy local
     - Test qua port 80: ss -lntp | grep haproxy
     - Test qua HAProxy: curl -I http://127.0.0.1
   - Tạo Subdomain:
     ![Ảnh 31](?raw=1)  
     - Tạo Virtual IP trên Firewall, mở Port 80(HTTP), 443(HTTPS)
     ![Ảnh 32](?raw=1)  
     - Tạo Rule cho VIPs
     ![Ảnh 33](?raw=1)
     ![Ảnh 34](?raw=1)
     - Add trusted Domain:
       - Sửa file /nextcloud/nextcloud/config/config.php:
         - Di chuyển đến vị trí file: cd /nextcloud
         - Kiểm tra Config: ls nextcloud/config
         - Mở file bằng nano:  nano nextcloud/config/config.php
         - sửa trusted_domains: có dạng
           ```
            'trusted_domains' =>
              array (
                0 => 'localhost',
              ),
           ```
           Sửa thành:
           ```
            'trusted_domains' =>
            array (
              0 => 'localhost',
              1 => '127.0.0.1',
              2 => '172.16.20.35',
              3 => 'cloudnvt.km0.vn',
            ),
           ```
          - Lưu file, thoát và restart container
      - Tạo SSL cho web:
        - Mở Port 443 và map (giống phần trên)
        - Cài SSL
          - Cài Certbot trên VM
            - apt update
            - apt install -y certbot
          - Pause HAProxy để xin Cert: systemctl stop haproxy
          - Xin SSL cho domain: certbot certonly --standalone -d cloudnvt.km0.vn
          - Kiểm tra cert:
            - ls -l /etc/letsencrypt/live/cloudnvt.km0.vn/
            - openssl x509 -in /etc/letsencrypt/live/cloudnvt.km0.vn/fullchain.pem -noout -dates
           ![Ảnh 35](?raw=1)
        -  Tạo file PEM (cert) cho HAProxy:
           ```
           cat /etc/letsencrypt/live/cloudnvt.km0.vn/fullchain.pem \
               /etc/letsencrypt/live/cloudnvt.km0.vn/privkey.pem \
               > /etc/ssl/private/cloudnvt.pem
           
            chmod 600 /etc/ssl/private/cloudnvt.pem
           ```
        -  Cấu hình HAProxy HTTPS:
          - Mở file: nano /etc/haproxy/haproxy.cfg
          - Ghi đè:
           ```
           global
               log /dev/log local0
               maxconn 4096
            
           defaults
               log global
               mode http
               option httplog
               option forwardfor
               timeout connect 5s
               timeout client  60s
               timeout server  60s
            
           frontend http_in
               bind *:80
               redirect scheme https if !{ ssl_fc }
            
           frontend https_in
               bind *:443 ssl crt /etc/ssl/private/cloudnvt.pem
               http-request set-header X-Forwarded-Proto https
               http-request set-header X-Forwarded-Port 443
               http-request set-header X-Forwarded-For %[src]
            
               default_backend nextcloud_backend
            
           backend nextcloud_backend
               server nextcloud 127.0.0.1:8080 check
           ```
          - Chỉnh NextCloud cho HTTPS:
            - Mở config: nano nextcloud/config/config.php
            - Thêm:
             ```
             'trusted_proxies' => ['127.0.0.1'],
             'overwriteprotocol' => 'https',
             'overwrite.cli.url' => 'https://cloudnvt.km0.vn',
             ```
             ![Ảnh 36](?raw=1)
            - Restart: docker restart nextcloud_app
          - Chỉnh KeyCloak cho HTTPS:
            - Kiểm tra DNS: nslookup auth.cloudnvt.km0.vn (trả về đúng IP WAN)
            - Cấp SSL cho KeyCloak:
              - Dừng HAProxy tạm thời: systemctl stop haproxy
              - Xin cert Let’s Encrypt cho auth: certbot certonly --standalone -d auth.cloudnvt.km0.vn
              - Kiểm tra cert vừa cấp: ls -l /etc/letsencrypt/live/auth.cloudnvt.km0.vn/
              - Kiểm tra xem HAProxy đã có PEM cho auth chưa: ls -l /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem
                - Nếu chưa có, tạo mới:
                  ```
                    cat /etc/letsencrypt/live/auth.cloudnvt.km0.vn/fullchain.pem \
                    /etc/letsencrypt/live/auth.cloudnvt.km0.vn/privkey.pem \
                    > /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem
                  ```
                  ```
                  chmod 600 /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem
                  ```
              - Verify PEM: openssl x509 -in /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem -noout -text | grep DNS (Phải thấy: DNS:auth.cloudnvt.km0.vn)
            - Sửa HAProxy:
              - Backup config trước khi sửa: cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
              - Mở config: nano /etc/haproxy/haproxy.cfg
              - Sửa thành:
             ```
             global
                log /dev/log local0
                log /dev/log local1 notice
                daemon
                maxconn 4096
            
            defaults
                log global
                mode http
                option httplog
                option dontlognull
                timeout connect 5s
                timeout client  60s
                timeout server  60s
            
            frontend http_in
                bind *:80
                redirect scheme https code 301 if !{ ssl_fc }
            
            frontend https_in
                bind *:443 ssl crt /etc/haproxy/certs/cloudnvt.km0.vn.pem crt /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem
                mode http
                option forwardfor
            
                acl host_nextcloud hdr(host) -i cloudnvt.km0.vn
                acl host_keycloak  hdr(host) -i auth.cloudnvt.km0.vn
            
                http-request set-header X-Forwarded-Proto https
                http-request set-header X-Forwarded-Port 443
                http-request set-header X-Forwarded-For %[src]
            
                use_backend nextcloud_backend if host_nextcloud
                use_backend keycloak_backend  if host_keycloak
                default_backend nextcloud_backend
            
            backend nextcloud_backend
                mode http
                server nextcloud 127.0.0.1:8080 check
            
            backend keycloak_backend
                mode http
                server keycloak 127.0.0.1:8081 check

             ```
              - Kiểm tra: haproxy -c -f /etc/haproxy/haproxy.cfg
              - Restart: systemctl restart haproxy
              - Kiểm tra Service: systemctl status haproxy
              - Kiểm tra KeyCloak:
                - Kiểm tra container: docker ps | grep keycloak (Thấy image: quay.io/keycloak/keycloak)
                - Test KeyCloak: curl -I http://127.0.0.1:8081
                - Test qua HAProxy: curl -kI https://auth.cloudnvt.km0.vn
              - Cấu hình KeyCloak chạy sau HAProxy
                - MỞ FILE docker-compose: ls docker-compose.yml

   



    

 
