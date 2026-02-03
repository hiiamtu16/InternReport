#KEYCLOAK
## Khởi tạo KeyCloak
  - Tạo thư mục riêng cho Keycloak
    ```
    mkdir -p /root/keycloak
    cd /root/keycloak
    ```
  - Tạo file Docker Compose cho Keycloak:
    ```
    nano compose.yml
    ```
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
        image: quay.io/keycloak/keycloak:latest
        container_name: keycloak
        restart: always
        command: start-dev
        ports:
          - "8081:8080"
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
    ```
    docker compose up -d
    ```
    - Kiểm tra:
      ```
      docker ps
      ```
    - Vào Web GUI: `http://172.16.20.35:8081/` (giao thức http; ip máy; port 8080 (có thể thay đổi trên file .yml)
      - Đăng nhập lần đầu:
      ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/2.png?raw=1)
      - Đổi pass admin
      ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/15.png?raw=1)
      ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/16.png?raw=1)
      ![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/17.png?raw=1)
  - Backup KeyCloak
    - backup database:
      ```
      tar czvf keycloak_backup.tar.gz postgres compose.yml
      ```
    - Restore:
      ```
      docker compose up -d
      ```

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
  ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/3.png?raw=1)
  ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/4.png?raw=1)
  - Tạo Client cho NextCloud
  ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/5.png?raw=1)
  ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/6.png?raw=1)
  ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/7.png?raw=1)
  ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/8.png?raw=1)
  - Lấy Client Secret
  ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/9.png?raw=1)
  - Tạo account
    - Tạo user 
    ![Ảnh 19](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/19.png?raw=1)
    ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/11.png?raw=1)
    - Tạo password
    ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/12.png?raw=1)
    ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/13.png?raw=1)
    ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/14.png?raw=1)

  ### Cấu hình NextCloud
  - Cài app Social Login
  ![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/18.png?raw=1)
  ![Ảnh 19](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/19.png?raw=1)
  - Cấu hình OpenID Connect
  ![Ảnh 20](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/20.png?raw=1)
    - Internal name: `Keycloak`
    - Title: `KeyCloak`
    - Authorize url: `http://172.16.20.35:8081/realms/nextcloud/protocol/openid-connect/auth?prompt=login` (mỗi lần đăng nhập bên KeyCloak được lựa chọn nhập pass hoặc đăng nhập mới)
    - Token url: `http://172.16.20.35:8081/realms/nextcloud/protocol/openid-connect/token`
    - Display name claim (optional): `name`
    - User info URL (optional): `http://172.16.20.35:8081/realms/nextcloud/protocol/openid-connect/userinfo`
    - Logout URL (optional):
    - Client Id: `nextcloud`
    - Client Secret: (Xem ở KeyCloak Client)
    - Scope: `openid profile email`
  - Cấu hình NextCloud nhận full name
    - Tạo Client Scope
    ![Ảnh 22](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/22.png?raw=1)
    ![Ảnh 23](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/23.png?raw=1)
    - Add Mapper
    ![Ảnh 24](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/24.png?raw=1)
    ![Ảnh 25](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/25.png?raw=1)
    ![Ảnh 26](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/26.png?raw=1)
    - Add Scope vào Cloud
    ![Ảnh 27](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/27.png?raw=1)
    ![Ảnh 28](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/28.png?raw=1)
    ![Ảnh 29](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/29.png?raw=1)
    - Check lại NextCloud đảm bảo chọn dòng: Display name claim (optional): name
    ![Ảnh 30](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/30.png?raw=1)
  - Kiểm tra: Mở Web NextCloud
  ![Ảnh 21](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/21.png?raw=1)
