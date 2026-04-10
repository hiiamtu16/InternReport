# MEETING

## 1. Update & Install Ubuntu
- Cài gói nền:
```
sudo apt update
sudo apt install -y ca-certificates curl gnupg
```
- Thêm Docker repo
```
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- Cài Docker engine & Docker compose
```
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
- Bật Docker
```
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```
- Test
```
sudo docker run hello-world
docker compose version
```
- Cho user chạy Docker k cần `sudo`
```
sudo usermod -aG docker $USER
newgrp docker
```

## 2. Cài HPB bằng aio-talk
- Tạo folder
```
sudo mkdir -p /opt/aio-talk
sudo mkdir -p /opt/coturn/certs
sudo mkdir -p /opt/nextcloud-talk-recording/tmp
sudo chown -R $USER:$USER /opt/aio-talk /opt/coturn /opt/nextcloud-talk-recording
```
- Tạo secret 4 secret cho `TURN_SECRET`, `SIGNALING_SECRET`, `INTERNAL_SECRET`, `RECORDING_SECRET`: chạy 4 lần lệnh
```
openssl rand -hex 32
```
- 4 secret lần lượt:
```
TURN_SECRET = faf2b3128b05f1ae167dd4794e8004a8ec0d8e9468aca882fd155f5b5a341116
SIGNALING_SECRET = 0bcc022acfc9383b0aebdaabad809e6ae5b6342ec26e4dc34ae3045e8bb7dce3
INTERNAL_SECRET = 7e8a87a949d20301d2c12a146b20a9317c1ae51fc504bcdec625d3b27265f8b7
RECORDING_SECRET = faf0067ba323a0c894217a5698c08e8e2628ee987c19728e032fc244e7e01d35
```
- Tạo file compose cho HPB:
```
sudo nano /opt/aio-talk/docker-compose.yml
```
- Nội dung:
```
services:
  aio-talk:
    image: nextcloud/aio-talk:latest
    container_name: aio-talk
    restart: unless-stopped
    network_mode: host
    environment:
      NEXTCLOUD_URL: https://cloudnvt.km0.vn
      NC_DOMAIN: cloudnvt.km0.vn

      TALK_HOST: meet.cloudnvt.km0.vn
      TALK_PORT: 3478

      TURN_SECRET: faf2b3128b05f1ae167dd4794e8004a8ec0d8e9468aca882fd155f5b5a341116
      SIGNALING_SECRET: 0bcc022acfc9383b0aebdaabad809e6ae5b6342ec26e4dc34ae3045e8bb7dce3
      INTERNAL_SECRET: 7e8a87a949d20301d2c12a146b20a9317c1ae51fc504bcdec625d3b27265f8b7

      TALK_MODE: external
      PUBLIC_WSS_URL: wss://meet.cloudnvt.km0.vn/spreed
      TRUSTED_PROXIES: 172.16.20.35
```
- Chạy:
```
cd /opt/aio-talk
docker compose pull
docker compose up -d
docker logs aio-talk --tail 50
```
- Test local:
```
curl http://127.0.0.1:8091/api/v1/welcome
```

## 3. Cài Coturn bằng Docker
- Tạo file:
```
sudo nano /opt/coturn/turnserver.conf
```
- Nội dung:
```
listening-port=3478
tls-listening-port=5349

listening-ip=172.16.20.36
relay-ip=172.16.20.36
external-ip=210.245.58.109/172.16.20.36

realm=cloudnvt.km0.vn
server-name=turn.cloudnvt.km0.vn

use-auth-secret
static-auth-secret=faf2b3128b05f1ae167dd4794e8004a8ec0d8e9468aca882fd155f5b5a341116

fingerprint
stale-nonce
no-multicast-peers

min-port=49160
max-port=49200

allowed-peer-ip=172.16.20.36
denied-peer-ip=0.0.0.0-0.255.255.255
denied-peer-ip=10.0.0.0-10.255.255.255
denied-peer-ip=100.64.0.0-100.127.255.255
denied-peer-ip=127.0.0.0-127.255.255.255
denied-peer-ip=169.254.0.0-169.254.255.255
denied-peer-ip=172.16.0.0-172.31.255.255
denied-peer-ip=192.0.0.0-192.0.0.255
denied-peer-ip=192.0.2.0-192.0.2.255
denied-peer-ip=192.88.99.0-192.88.99.255
denied-peer-ip=192.168.0.0-192.168.255.255
denied-peer-ip=198.18.0.0-198.19.255.255
denied-peer-ip=198.51.100.0-198.51.100.255
denied-peer-ip=203.0.113.0-203.0.113.255
denied-peer-ip=240.0.0.0-255.255.255.255

cert=/etc/coturn/certs/fullchain.pem
pkey=/etc/coturn/certs/privkey.pem
```
- Tạo file
```
sudo nano /opt/coturn/docker-compose.yml
```
- Nội dung:
```
services:
  coturn:
    image: coturn/coturn:latest
    container_name: coturn
    network_mode: host
    restart: unless-stopped
    volumes:
      - ./turnserver.conf:/etc/coturn/turnserver.conf:ro
      - ./certs:/etc/coturn/certs:ro
```
- Chạy
```
cd /opt/coturn
docker compose pull
docker compose up -d
docker logs coturn --tail 50
```

## 4. Cài Recording Backend
- Tạo file
```
sudo nano /opt/nextcloud-talk-recording/docker-compose.yml
```
- Nội dung
```
services:
  talk-recording:
    image: nextcloud/aio-talk-recording:latest
    container_name: talk-recording
    restart: unless-stopped
    shm_size: "2gb"
    ports:
      - "8092:1234"
    environment:
      NC_DOMAIN: cloudnvt.km0.vn
      HPB_DOMAIN: meet.cloudnvt.km0.vn
      RECORDING_SECRET: faf0067ba323a0c894217a5698c08e8e2628ee987c19728e032fc244e7e01d35
      INTERNAL_SECRET: 7e8a87a949d20301d2c12a146b20a9317c1ae51fc504bcdec625d3b27265f8b7
      NC_PROTOCOL: https
      HPB_PROTOCOL: https
      HPB_PATH: /standalone-signaling/
      SKIP_VERIFY: "false"
      ALLOW_ALL: "false"
    volumes:
      - ./tmp:/tmp
```
- Chạy
```
cd /opt/nextcloud-talk-recording
docker compose pull
docker compose up -d
docker logs talk-recording --tail 50
```

## 5. Cấu hình tiếp
### 5.1 Mở Port NAT trên firewall
- Mở các port nat về IP của máy đang chạy meet: `172.16.20.36`
```
3478/tcp
3478/udp
5349/tcp
49160-49200/udp
```

### 5.2 Cấu hình HAProxy trên 172.16.20.35
- Xin cert cho `meet` và `record`
  - Tạm dừng HAProxy
   ```
   sudo systemctl stop haproxy
   ```
  - Xin Cert
   ```
   sudo certbot certonly --standalone \
    -d cloudnvt.km0.vn \
    -d meet.cloudnvt.km0.vn \
    -d record.cloudnvt.km0.vn
   ```
  - Ghép Cert
   ```
   sudo bash -c 'cat \
    /etc/letsencrypt/live/cloudnvt.km0.vn/fullchain.pem \
    /etc/letsencrypt/live/cloudnvt.km0.vn/privkey.pem \
    > /etc/haproxy/certs/cloudnvt.km0.vn.pem'
   ```
  - Set quyền:
   ```
   sudo chmod 600 /etc/haproxy/certs/cloudnvt.km0.vn.pem
   sudo chown root:root /etc/haproxy/certs/cloudnvt.km0.vn.pem
   ```
  - Chạy lại HAProxy
   ```
   sudo haproxy -c -f /etc/haproxy/haproxy.cfg
   sudo systemctl start haproxy
   ```
  - Kiểm tra cert đã đúng chưa
   ```
   curl -I https://cloudnvt.km0.vn
   curl -I https://meet.cloudnvt.km0.vn
   curl -I https://record.cloudnvt.km0.vn
   ```
- Cấu hình lại HAProxy
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
      acl host_meet hdr(host) -i meet.cloudnvt.km0.vn
      acl host_record hdr(host) -i record.cloudnvt.km0.vn
  
      use_backend nextcloud_backend if host_nextcloud
      use_backend meet_backend if host_meet
      use_backend record_backend if host_record
      default_backend nextcloud_backend
  
  backend nextcloud_backend
      mode http
      server nextcloud 127.0.0.1:8080 check
  
  backend meet_backend
      mode http
      option http-server-close
      timeout connect 10s
      timeout server 1h
      timeout tunnel 1h
      server meet 172.16.20.36:8081 check
  
  backend record_backend
      mode http
      timeout connect 10s
      timeout server 300s
      server record 172.16.20.36:8092 check
   ```
  - Áp dụng cấu hình
   ```
   sudo haproxy -c -f /etc/haproxy/haproxy.cfg
   sudo systemctl start haproxy
   sudo systemctl reload haproxy
   systemctl status haproxy
   ```
  - Test
   ```
   curl https://meet.cloudnvt.km0.vn/api/v1/welcome
   ```
- Sửa `/etc/hosts`
  ```
  sudo nano /etc/hosts
  ```
- Đổi `127.0.0.1 tunv-meet` thành
  ```
  172.16.20.36 tunv-meet
  ```
- Kiểm tra và restart lại `aoi-talk`
```
hostname -i
getent hosts $(hostname)
```
```
cd /opt/aio-talk
docker compose down
docker compose up -d
docker logs aio-talk --tail 50
```

## 6. Cấu hình trên Nextcloud
![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/3bb3dea458040bc5c692e849bbbe9ef71a31decc/Picture%20/Service/Nextcloud_Meeting/1.png?raw=1)
![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/3bb3dea458040bc5c692e849bbbe9ef71a31decc/Picture%20/Service/Nextcloud_Meeting/2.png?raw=1)
![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/3bb3dea458040bc5c692e849bbbe9ef71a31decc/Picture%20/Service/Nextcloud_Meeting/3.png?raw=1)
![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/3bb3dea458040bc5c692e849bbbe9ef71a31decc/Picture%20/Service/Nextcloud_Meeting/4.png?raw=1)






