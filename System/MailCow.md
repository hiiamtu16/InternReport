# MAILCOW

## Cài đặt MailCow trên máy Ubuntu
  - CÀI DOCKER + DEPENDENCIES CHO MAILCOW
    - Update hệ thống:
      ``` apt update
      apt update -y
      ```
    - Cài JQ:
      ```
      apt install -y jq
      ```
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
    ``` cd /opt
    git clone https://github.com/mailcow/mailcow-dockerized
    cd mailcow-dockerized
    ```
  - KHỞI TẠO CẤU HÌNH MAILCOW:
    ```
    ./generate_config.sh
    ```
    - Script sẽ hỏi: ` Mail server hostname (FQDN):`
    - Điền: `mail.cloudnvt.km0.vn`
  - CHỈNH CẤU HÌNH TỐI THIỂU
    - Mở file cấu hình:
      ```
      nano mailcow.conf
      ```
    - Thêm tạm:
      ```
      SKIP_LETS_ENCRYPT=y
      ```
      (tạm thời bỏ qua DNS)
    - Lưu file rồi chạy Mailcow lần đầu
      ``` docker compose pull
      docker compose up -d
      ```
 ---
 ## Cấu hình DNS:
   - Tạo thêm bản ghi cho MailCow (dựa trên bản ghi đã có của NextCloud và KeyCloak)
    ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/bdc1788022cca68d1a74f19d08a1f702a228a2d7/Picture%20/Service/MailCow/1.png?raw=1)
   - Tạo NAT và Rule cho MailCow
     - Tạo VIPs và mở Port dịch vụ
     ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/bdc1788022cca68d1a74f19d08a1f702a228a2d7/Picture%20/Service/MailCow/2.png?raw=1)
     - Add VIPs vào Rule
     ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/bdc1788022cca68d1a74f19d08a1f702a228a2d7/Picture%20/Service/MailCow/3.png?raw=1)
     - Kiểm tra:
       ```
       nc -vz mail.cloudnvt.km0.vn 25
       ```
        (thấy success / có thể test các port khác)

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
    ``` apt update
    apt install -y certbot
    Kiểm tra: certbot --version
    ```
  - Kiểm tra Cert:
    ```
    certbot certificates
    ```
  - Xin Cert:
  ```
  certbot certonly \
    --manual \
    --preferred-challenges dns \
    -d mail2.cloudnvt.km0.vn
  ```
  
  ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/bdc1788022cca68d1a74f19d08a1f702a228a2d7/Picture%20/Service/MailCow/15.png?raw=1)
  - Tạo bản ghi TXT trên DNS giống với CertBot gửi thông tin
  ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/bdc1788022cca68d1a74f19d08a1f702a228a2d7/Picture%20/Service/MailCow/16.png?raw=1)
  - Kiểm tra bản ghi TXT trên 1 PuTTY khác (giống, ok ==> quay lại PuTTY xin cert Enter): `dig TXT _acme-challenge.mail2.cloudnvt.km0.vn +short`
  ![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/bdc1788022cca68d1a74f19d08a1f702a228a2d7/Picture%20/Service/MailCow/17.png?raw=1)
  - Kiểm tra Cert:
    ```
    certbot certificates
    ```
  ![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/bdc1788022cca68d1a74f19d08a1f702a228a2d7/Picture%20/Service/MailCow/18.png?raw=1)
  - Xác nhận cert tồn tại:
    ```
    ls -l /etc/letsencrypt/live/mail.cloudnvt.km0.vn/
    ```

---
