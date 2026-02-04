# HƯỚNG DẪN CÀI ĐẶT NEXTCLOUD BẰNG DOCKER COMPOSE

## 1. Chuẩn bị hệ thống

### 1.1 Khởi tạo máy Ubuntu

Sử dụng Ubuntu Server 22.04 LTS  
Máy có kết nối Internet  
Có quyền root hoặc sudo  

---

## 2. Cài đặt Docker và Docker Compose

### 2.1 Cập nhật hệ thống và cài gói cần thiết

```bash
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release -y
```

### 2.2 Thêm Docker GPG key và repository
```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 2.3 Cài Docker Engine và Docker Compose plugin
```
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
```
### 2.4 Kiểm tra cài đặt
```
docker --version
docker compose version
```
### 2.5 Cho phép user chạy Docker không cần sudo
```
sudo usermod -aG docker $USER
newgrp docker
```
### 2.6 Khởi động Docker và cho chạy cùng hệ thống
```
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```
## 3. Triển khai Nextcloud bằng Docker Compose
### 3.1 Tạo thư mục làm việc
```
mkdir -p ~/nextcloud
cd ~/nextcloud
```
### 3.2 Tạo file Docker Compose
```
nano compose.yml
```
- Nội dung file:
```
services:
  db:
    image: mariadb:10.11
    container_name: nextcloud_db
    restart: unless-stopped
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
    restart: unless-stopped
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
### 3.3 Khởi động Nextcloud
```
docker compose up -d
```
### 3.4 Kiểm tra container
```
docker ps
```
- Phải thấy container
  - nextcloud_app
  - nextcloud_db
### 3.5 Truy cập giao diện web
- `http://IP_MAY_CHU:8080`
- Đăng nhập lần đầu: ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/efa8f6e07ccc12b230193375c8ca804d043a4381/Picture%20/Service/NextCloud%2C%20KeyCloak%2C%20HAProxy/1.png?raw=1)
- Đổi pass admin

## 4. Cấu hình Nextcloud khi chạy sau HAProxy HTTPS
### 4.1 Sửa file config.php
```
docker exec -it nextcloud_app bash
nano /var/www/html/config/config.php
```
- Đảm bảo có các cấu hình sau:
  ```
  'trusted_domains' =>
  array (
    0 => 'cloudnvt.km0.vn',
  ),
  ```
  ```
  'trusted_proxies' =>
  array (
    0 => '127.0.0.1',
  ),
  ```
  ```
  'overwritehost' => 'cloudnvt.km0.vn',
  'overwriteprotocol' => 'https',
  ```
- Lưu file và thoát
### 4.2 Fix quyền thư mục Nextcloud
```
docker exec -u root -it nextcloud_app bash -lc "chown -R www-data:www-data /var/www/html && find /var/www/html -type d -exec chmod 750 {} \; && find /var/www/html -type f -exec chmod 640 {} \;"
```
### 4.3 Restart Nextcloud
```
docker restart nextcloud_app
```
### 4.4 Kiểm tra
- Kiểm tra ip:
```
curl -I http://127.0.0.1:8080
```
- Kiểm tra domain: truy cập `https://cloudnvt.km0.vn`
