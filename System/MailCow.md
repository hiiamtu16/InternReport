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
   - Bật LET’S ENCRYPT cho MailCow:
     - Mở cấu hình Mailcow trên VM:
       - cd /opt/mailcow-dockerized
       - nano mailcow.conf
     - Tìm dòng: SKIP_LETS_ENCRYPT=y
     - Sửa thành: SKIP_LETS_ENCRYPT=n
     - Restart Mailcow để xin cert:
       - docker compose down
       - docker compose up -d
       - Kiểm tra log xin cert: docker compose logs -f acme-mailcow
 
 
