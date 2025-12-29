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
 ## HTTPS cho MailCow trên HAProxy
   - Kiểm tra IP WAN MailCow: dig mail.cloudnvt.km0.vn +short
   - Trên máy HAProxy
     - systemctl stop haproxy
     - Check Port 80: ss -lntp | grep ':80'
     - Xin Cert: certbot certonly --standalone -d mail.cloudnvt.km0.vn
     - Kiểm tra Cert: ls -l /etc/letsencrypt/live/mail.cloudnvt.km0.vn/
     - Ghép Cert cho HAProxy:
       ```
       cat \
        /etc/letsencrypt/live/mail.cloudnvt.km0.vn/fullchain.pem \
        /etc/letsencrypt/live/mail.cloudnvt.km0.vn/privkey.pem \
        > /etc/haproxy/certs/mail.cloudnvt.km0.vn.pem
        
        chmod 600 /etc/haproxy/certs/mail.cloudnvt.km0.vn.pem
       ```
     - Reload HAProxy:
       - haproxy -c -f /etc/haproxy/haproxy.cfg
       - systemctl reload haproxy
     - Test kết quả cuối: curl -Ik https://mail.cloudnvt.km0.vn
     - Sửa HAProxy:
       - Mở file: nano /etc/haproxy/haproxy.cfg
       -  
 

