# HƯỚNG DẪN XÁC MINH ĐĂNG NHẬP MAILCOW QUA KEYCLOAK, ADD MAIL SERVER VÀO NEXTCLOUD

## Cấu hình trên Keycloak:
- Tạo Client `mailcow`
  ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/1.png?raw=1)
  ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/2.png?raw=1)
  ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/3.png?raw=1)
- Bật `Service Account Role`:
  ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/4.png?raw=1)
  ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/5.png?raw=1)
- Tạo `user_attribute`:
  - BƯỚC 1: Tạo Client Scope cho Mailcow Password
  ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/6.png?raw=1)
  ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/7.png?raw=1)
  - BƯỚC 2: Tạo Mapper cho password
  ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/8.png?raw=1)
  ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/9.png?raw=1)
  ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/10.png?raw=1)
  - BƯỚC 3: Gán Client Scope vào Client `mailcow`
  ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/11.png?raw=1)
  - BƯỚC 4: Tạo attribute cho USER
  ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/12.png?raw=1)
  ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/13.png?raw=1)
  - BƯỚC 5: Gán attribute cho USER
    - Tạo mã hash trên server Mailcow:
      - Tìm Container `dovecot`:
        ```
        docker ps --format "table {{.Names}}\t{{.Image}}"
        ```
        ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/14.png?raw=1)
      - Chạy lệnh lấy mã hash:
        ```
        docker exec -it mailcowdockerized-dovecot-mailcow-1 \
        doveadm pw -s BLF-CRYPT
        ```
      - Nhập password của user vào để lấy mã hash
        ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/15.png?raw=1)
        ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/16.png?raw=1)

## Cấu hình trên MailCow
- Kiểm tra Domain Mail
![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/17.png?raw=1)
- Kết nối Mailcow với Keycloak
  - Lấy Secret client:
   ![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/18.png?raw=1)
  - Cấu hình connect Mailcow - Keycloak
   ![Ảnh 19](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/19.png?raw=1)
- Sync lại user:
  ```
  cd /opt/mailcow-dockerized
  docker compose restart
  ```
- Kiểm tra login
   ![Ảnh 20](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/20.png?raw=1)
   ![Ảnh 21](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/21.png?raw=1)

## Cấu hình Mail trên Nextcloud
- Cấu hình thủ công
   ![Ảnh 22](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/22.png?raw=1)
   ![Ảnh 23](https://github.com/hiiamtu16/InternReport/blob/8c45c3099e248952e48466d1437119bb40ab026e/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/23.png?raw=1)

- Cấu hình tự động:
  - Tạo bản ghi MX
  ![Ảnh 24](https://github.com/hiiamtu16/InternReport/blob/0c29cc1078e117902a8b5e84fa8502403239e697/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/24.png?raw=1)
  - Login Mail Auto
  ![Ảnh 25](https://github.com/hiiamtu16/InternReport/blob/0c29cc1078e117902a8b5e84fa8502403239e697/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/25.png?raw=1)
  ![Ảnh 26](https://github.com/hiiamtu16/InternReport/blob/0c29cc1078e117902a8b5e84fa8502403239e697/Picture%20/Service/Li%C3%AAn%20k%E1%BA%BFt%20Mail-Cloud-Keycloak/26.png?raw=1)
