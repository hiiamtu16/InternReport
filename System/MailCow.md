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
