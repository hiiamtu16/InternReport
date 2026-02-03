## HA Proxy
   - Cài HA Proxy:
     - Lệnh cài:
       ```
       sudo apt update
       sudo apt install -y haproxy
       ```
     - Kiểm tra:
       ```
       haproxy -v
       ```
   - Tắt HAProxy mặc định (chưa cấu hình):
     ```
     sudo systemctl stop haproxy
     ```
   - Cấu hình HAProxy
     - Backup config:
       ```
       sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
       ```
     - Mở file cấu hình:
       ```
       sudo nano /etc/haproxy/haproxy.cfg
       ```
     - Xoá và paste đoạn này:
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
       ```
       sudo systemctl start haproxy
       sudo systemctl status haproxy
       ```
   - Test HAProxy local
     - Test qua port 80:
       ```
       ss -lntp | grep haproxy
       ```
     - Test qua HAProxy:
       ```
       curl -I http://127.0.0.1
       ```
   - Tạo Subdomain:
     ![Ảnh 31](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/31.png?raw=1)  
     - Tạo Virtual IP trên Firewall, mở Port 80(HTTP), 443(HTTPS)
     ![Ảnh 32](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/32.png?raw=1)  
     - Tạo Rule cho VIPs
     ![Ảnh 33](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/33.png?raw=1)
     ![Ảnh 34](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/34.png?raw=1)
     - Add trusted Domain:
       - Sửa file `/nextcloud/nextcloud/config/config.php`:
         - Di chuyển đến vị trí file:
           ```
           cd ~/nextcloud
           ```
         - Kiểm tra Config:
           ```
           ls nextcloud/config
           ```
         - Mở file bằng nano:
           ```
           nano nextcloud/config/config.php
           ```
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
          - Lưu file, thoát và restart container:
            ```
            docker compose restart
            ```
      - Tạo SSL cho web:
        - Mở Port 443 và map (giống phần trên)
        - Cài SSL
          - Cài Certbot trên VM
            ```
            apt update
            apt install -y certbot
            ```
          - Pause HAProxy để xin Cert:
            ```
            systemctl stop haproxy
            ```
          - Xin SSL cho domain:
            ```
            certbot certonly --standalone -d cloudnvt.km0.vn
            ```
          - Kiểm tra cert:
            ```
            ls -l /etc/letsencrypt/live/cloudnvt.km0.vn/
            openssl x509 -in /etc/letsencrypt/live/cloudnvt.km0.vn/fullchain.pem -noout -dates
            ```
          - Tạo file
            ```
            mkdir -p /etc/haproxy/certs
            ```
          - Ghép cert cho domain:
            ```
            sudo cat \
            /etc/letsencrypt/live/cloudnvt.km0.vn/fullchain.pem \
            /etc/letsencrypt/live/cloudnvt.km0.vn/privkey.pem \
            > /etc/haproxy/certs/cloudnvt.km0.vn.pem
            ```
          - Set quyền:
            ```
            chmod 600 /etc/haproxy/certs/cloudnvt.km0.vn.pem
            chown root:root /etc/haproxy/certs/cloudnvt.km0.vn.pem
            ```
           ![Ảnh 35](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/35.png?raw=1)
        -  Tạo file PEM (cert) cho HAProxy:
           ```
           cat /etc/letsencrypt/live/cloudnvt.km0.vn/fullchain.pem \
               /etc/letsencrypt/live/cloudnvt.km0.vn/privkey.pem \
               > /etc/ssl/private/cloudnvt.pem
           
            chmod 600 /etc/ssl/private/cloudnvt.pem
           ```
        -  Cấu hình HAProxy HTTPS:
          - Mở file:
            ```
            nano /etc/haproxy/haproxy.cfg
            ```
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
            - Mở config:
              ```
              nano nextcloud/config/config.php
              ```
            - Thêm:
             ```
             'trusted_proxies' => ['127.0.0.1'],
             'overwriteprotocol' => 'https',
             'overwrite.cli.url' => 'https://cloudnvt.km0.vn',
             ```
             ![Ảnh 36](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/36.png?raw=1)
            - Restart:
              ```
              docker restart nextcloud_app
              ```
          - Chỉnh KeyCloak cho HTTPS:
            - Kiểm tra DNS:
              ```
              nslookup auth.cloudnvt.km0.vn
              ```
              (trả về đúng IP WAN)
            - Cấp SSL cho KeyCloak:
              - Dừng HAProxy tạm thời:
                ```
                systemctl stop haproxy
                ```
              - Xin cert Let’s Encrypt cho auth:
                ```
                certbot certonly --standalone -d auth.cloudnvt.km0.vn
                ```
              - Kiểm tra cert vừa cấp:
                ```
                ls -l /etc/letsencrypt/live/auth.cloudnvt.km0.vn/
                ```
              - Kiểm tra xem HAProxy đã có PEM cho auth chưa:
                ```
                ls -l /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem
                ```
                - Tạo thư mục certs cho HAProxy:
                  ```
                  sudo mkdir -p /etc/haproxy/certs
                  ```
                - Ghép cert HAProxy: 
                  ```
                  sudo cat \
                  /etc/letsencrypt/live/auth.cloudnvt.km0.vn/fullchain.pem \
                  /etc/letsencrypt/live/auth.cloudnvt.km0.vn/privkey.pem \
                  > /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem
                  ```
                 - Set quyền đọc cho haproxy:
                   ```
                   sudo chmod 600 /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem
                   sudo chown root:root /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem
                   ```
              - Verify PEM:
                ```
                openssl x509 -in /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem -noout -text | grep DNS
                ```
                (Phải thấy: DNS:auth.cloudnvt.km0.vn)
            - Sửa HAProxy:
              - Backup config trước khi sửa:
                ```
                cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
                ```
              - Mở config:
                ```
                nano /etc/haproxy/haproxy.cfg
                ```
              - Sửa thành:
             ```
             global
                log /dev/log local0
                log /dev/log local1 notice
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
              - Kiểm tra:
                ```
                haproxy -c -f /etc/haproxy/haproxy.cfg
                ```
              - Restart:
                ```
                systemctl restart haproxy
                ```
              - Kiểm tra Service:
                ```
                systemctl status haproxy
                ```
              - Kiểm tra KeyCloak:
                - Kiểm tra container:
                  ```
                  docker ps | grep keycloak
                  ```
                   (Thấy image: quay.io/keycloak/keycloak)
                - Test KeyCloak:
                  ```
                  curl -I http://127.0.0.1:8081
                  ```
                - Test qua HAProxy:
                  ```
                  curl -kI https://auth.cloudnvt.km0.vn
                  ```
              - Cấu hình KeyCloak chạy sau HAProxy
                - Mở file compose.yml của Keycloak:
                  ```
                  cd /root/keycloak
                  nano compose.yml
                  ```
                - Sửa service keycloak thành như sau:
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
                    command: start
                    ports:
                     - "8081:8080"
                    environment:
                      KC_DB: postgres
                      KC_DB_URL_HOST: postgres
                      KC_DB_USERNAME: keycloak
                      KC_DB_PASSWORD: keycloakpassword
                
                      KC_PROXY: edge
                      KC_HTTP_ENABLED: true
                      KC_HOSTNAME: auth.cloudnvt.km0.vn
                      KC_HOSTNAME_STRICT: true
                      KC_HOSTNAME_STRICT_HTTPS: true
                      KC_FEATURES: hostname:v1
                
                      KEYCLOAK_ADMIN: admin
                      KEYCLOAK_ADMIN_PASSWORD: adminpassword
                    depends_on:
                      - postgres
                 ``` 
                - Restart KeyCloak:
                  ```
                  cd /root/keycloak
                  docker compose down
                  docker compose up -d
                  ```
                - RESTART HAPROXY:
                  ```
                  haproxy -c -f /etc/haproxy/haproxy.cfg
                  systemctl restart haproxy
                  ```
              - Sửa KeyCloak Web GUI
                ![Ảnh 38](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/38.png?raw=1)
                ![Ảnh 39](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/39.png?raw=1)
              - Sửa NextCloud Web GUI: Social login:
                ![Ảnh 40](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/40.png?raw=1)
