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
    image: postgres:16
    container_name: nextcloud-db
    restart: always
    environment:
      POSTGRES_DB: nextcloud
      POSTGRES_USER: ncuser
      POSTGRES_PASSWORD: ncpass
    volumes:
      - ./postgres:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    container_name: nextcloud-redis
    restart: always
    command: >
      redis-server
      --save ""
      --appendonly no
      --maxmemory 8gb
      --maxmemory-policy noeviction
      --hz 50
      --io-threads 4
      --io-threads-do-reads yes

  app:
    image: nextcloud:latest
    container_name: nextcloud-app
    restart: always
    depends_on:
      - db
      - redis
    environment:
      POSTGRES_DB: nextcloud
      POSTGRES_USER: ncuser
      POSTGRES_PASSWORD: ncpass
      POSTGRES_HOST: db
      REDIS_HOST: redis
    ports:
      - "8080:80"
    volumes:
      - ./nextcloud/html:/var/www/html
      - ./nextcloud/config:/var/www/html/config
      - /mnt/nextcloud-data:/var/www/html/data
      - ./php/php.ini:/usr/local/etc/php/conf.d/php.ini
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

### 3.6 Xin HTTPS bằng certbot
- Cài certbot:
```
apt update
apt install -y snapd
snap install core
snap refresh core
snap install --classic certbot
ln -s /snap/bin/certbot /usr/bin/certbot
```
- Tạm dừng certbot:
```
systemctl stop haproxy
```
- Xin cert
```
certbot certonly --standalone -d cloudnvt.km0.vn
```
- Gộp chứng chỉ
```
mkdir -p /etc/haproxy/certs
cat /etc/letsencrypt/live/cloudnvt.km0.vn/privkey.pem /etc/letsencrypt/live/cloudnvt.km0.vn/fullchain.pem > /etc/haproxy/certs/cloudnvt.km0.vn.pem
chmod 600 /etc/haproxy/certs/cloudnvt.km0.vn.pem
```
- Sửa HAProxy
  - Mở file:
   ```
   nano /etc/haproxy/haproxy.cfg
   ```
  - Nội dung:
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
      timeout client 60s
      timeout server 60s
  
  frontend http_in
      bind *:80
      redirect scheme https code 301 if !{ ssl_fc }
  
  frontend https_in
      bind *:443 ssl crt /etc/haproxy/certs/cloudnvt.km0.vn.pem
  
      option forwardfor
      http-request set-header X-Forwarded-Proto https
      http-request set-header X-Forwarded-Port 443
      http-request set-header X-Forwarded-Host %[req.hdr(Host)]
  
      acl host_nextcloud hdr(host) -i cloudnvt.km0.vn
      use_backend nextcloud_backend if host_nextcloud
      default_backend nextcloud_backend
  
  backend nextcloud_backend
      mode http
      server nextcloud 127.0.0.1:8080 check
  ```
  - Kiểm tra:
   ```
   haproxy -c -f /etc/haproxy/haproxy.cfg
   ```
  - Khởi động lại
   ```
   systemctl start haproxy
   systemctl restart haproxy
   systemctl status haproxy
   ```
  - 

## 4. Cấu hình Nextcloud khi chạy sau HAProxy HTTPS
### 4.1 Sửa file config.php
```
nano /root/nextcloud/nextcloud/config/config.php
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
