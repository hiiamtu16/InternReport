# THÊM TÍNH NĂNG RECORD MEETING TRONG NEXTCLOUD CLOUD.APPS.VN

- Server thực hiện: 172.16.5.37
- Nội dung thực hiện:
  - Sửa file compose trong `/opt/` 
  - Chạy lại Docker `aio-talk` mới
  - Chạy lại Docker `coturn` mới
  - Tạo mới Docker `nextcloud-talk-recording`
  - Secret sử dụng:
  ```
  TURN_SECRET: 9f4a1a80138de42c76bc85800e6bac48e0d26f44cdddb473df6ae5fbd74ece32
  SIGNALING_SECRET: 9b402237c3ae664a916ea8c7c1d7b8113fb456a33fc3d9840639423d8718cd18
  INTERNAL_SECRET: f3658fb9ca86013958b757c89721e152a65e1b13a2a853a003a9a6f4c7e18cb3
  RECORD_SECRET: a8c73b31758f8564f645131cf4236466ccf970968a57bc039b2a4c6e9b26871c
  ```

## File Compose aio-talk
- Đường dẫn:
```
nano /opt/aio-talk/docker-compose.yml
```
- Nội dung file:
```
services:
  aio-talk:
    image: nextcloud/aio-talk:latest
    container_name: aio-talk
    restart: unless-stopped
    network_mode: host
    environment:
      NEXTCLOUD_URL: https://cloud.apps.vn
      NC_DOMAIN: cloud.apps.vn

      TALK_HOST: meet.apps.vn
      TALK_PORT: 3478

      TURN_SECRET: 9f4a1a80138de42c76bc85800e6bac48e0d26f44cdddb473df6ae5fbd74ece32
      SIGNALING_SECRET: 9b402237c3ae664a916ea8c7c1d7b8113fb456a33fc3d9840639423d8718cd18
      INTERNAL_SECRET: f3658fb9ca86013958b757c89721e152a65e1b13a2a853a003a9a6f4c7e18cb3

      TALK_MODE: external
      PUBLIC_WSS_URL: wss://meet.apps.vn/spreed
      TRUSTED_PROXIES: 127.0.0.1
```

## File Compose coturn
- Đường dẫn:
```
nano /opt/coturn/docker-compose.yml
```
- Nội dung file:
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

## File Compose nextcloud-talk-recording
- Đường dẫn:
```
nano /opt/nextcloud-talk-recording/docker-compose.yml 
```
- Nội dung file:
```
services:
  talk-recording:
    image: nextcloud/aio-talk-recording:latest
    container_name: talk-recording
    restart: unless-stopped
    ports:
      - "8092:1234"
    environment:
      NC_DOMAIN: cloud.apps.vn
      HPB_DOMAIN: meet.apps.vn
      RECORDING_SECRET: a8c73b31758f8564f645131cf4236466ccf970968a57bc039b2a4c6e9b26871c
      INTERNAL_SECRET: f3658fb9ca86013958b757c89721e152a65e1b13a2a853a003a9a6f4c7e18cb3
      NC_PROTOCOL: https
      HPB_PROTOCOL: https
      HPB_PATH: ""
      SKIP_VERIFY: "false"
      ALLOW_ALL: "false"
    extra_hosts:
      - "cloud.apps.vn:192.168.253.10"
      - "meet.apps.vn:172.16.5.37"
      - "record.apps.vn:172.16.5.37"
      - "turn.apps.vn:172.16.5.37"

```
## Cert, DNS, Public
- IP Public server meet: `210.245.58.97` (đã có sẵn)
- DNS Record:
  - meet.apps.vn - A Record (đã có sẵn)
  - turn.apps.vn - A Record (đã có sẵn)
  - record.apps.vn - A Record (đã có sẵn)
- Cert:
  - Đường dẫn:
   ```
   cat /opt/coturn/cert
   ```
  - Trạng thái: đã có sẵn

## Tạo reverse proxy Nginx 
- Cho `meet.apps.vn`:
  - Đường dẫn:
  ```
  sudo nano /etc/nginx/sites-available/meet.apps.vn
  ```
  - File:
  ```
  server {
      listen 80;
      listen [::]:80;
      server_name meet.apps.vn;
      return 301 https://$host$request_uri;
  }
  
  server {
      listen 443 ssl http2;
      listen [::]:443 ssl http2;
      server_name meet.apps.vn;
  
      ssl_certificate /etc/letsencrypt/live/meet.apps.vn/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/meet.apps.vn/privkey.pem;
  
      client_max_body_size 100M;
  
      location / {
          proxy_pass http://127.0.0.1:8081;
          proxy_http_version 1.1;
  
          proxy_set_header Host $host;
          proxy_set_header X-Forwarded-Proto https;
          proxy_set_header X-Forwarded-Port 443;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Real-IP $remote_addr;
  
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "upgrade";
  
          proxy_read_timeout 3600;
          proxy_send_timeout 3600;
          proxy_connect_timeout 60;
      }
  }
  ```
- Cho `record.apps.vn`:
  - Đường dẫn:
   ```
   sudo nano /etc/nginx/sites-available/record.apps.vn
   ```
  - File:
   ```
   server {
      listen 80;
      listen [::]:80;
      server_name record.apps.vn;
      return 301 https://$host$request_uri;
  }
  
  server {
      listen 443 ssl http2;
      listen [::]:443 ssl http2;
      server_name record.apps.vn;
  
      ssl_certificate /etc/letsencrypt/live/record.apps.vn/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/record.apps.vn/privkey.pem;
  
      client_max_body_size 100M;
  
      location / {
          proxy_pass http://127.0.0.1:8092;
          proxy_http_version 1.1;
  
          proxy_set_header Host $host;
          proxy_set_header X-Forwarded-Proto https;
          proxy_set_header X-Forwarded-Port 443;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Real-IP $remote_addr;
  
          proxy_read_timeout 300;
          proxy_send_timeout 300;
          proxy_connect_timeout 60;
      }
  }
   ```
  - Bật site và kiểm tra
   ```
   sudo ln -s /etc/nginx/sites-available/meet.apps.vn /etc/nginx/sites-enabled/meet.apps.vn
   sudo ln -s /etc/nginx/sites-available/record.apps.vn /etc/nginx/sites-enabled/record.apps.vn
   sudo nginx -t
   sudo systemctl reload nginx
   curl http://127.0.0.1:8081/api/v1/welcome
   curl -I http://127.0.0.1:8092
   curl https://meet.apps.vn/api/v1/welcome
   curl -I https://record.apps.vn
   ```

## Cấu hình trên Nextcloud
![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/427ffa1fc8edadd11d4a102a4c72b57c8eb77e29/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/System/Nextcloud_Meeting_Record/1.png?raw=1)
![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/427ffa1fc8edadd11d4a102a4c72b57c8eb77e29/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/System/Nextcloud_Meeting_Record/2.png?raw=1)
