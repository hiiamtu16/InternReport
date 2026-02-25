# SAMBA DC

## Cài Samba DC docker
  ### Cài Docker và Docker Compose
  - Cài Docker
  ```
  sudo apt update
  sudo apt install docker.io -y
  sudo systemctl enable docker
  sudo systemctl start docker
  ```
  - Cài Docker Compose plugin
  ```
  sudo apt install docker-compose-plugin -y
  ```
  - Kiểm tra
  ```
  docker version
  docker compose version
  ```
  ### Chuẩn bị thư mục chạy Samba DC
  - Tạo thư mục:
  ```
  mkdir -p /opt/samba-dc
  cd /opt/samba-dc
  ```
  - Tạo thư mục dữ liệu:
  ```
  mkdir data
  ```
  ### Tạo file docker-compose.yml
  - Tạo file
  ```
  nano docker-compose.yml
  ```
  - Nội dung
  ```
  version: "3.8"

services:
  samba-dc:
    image: instantlinux/samba-dc
    container_name: samba-dc
    hostname: dc1
    environment:
      REALM: OCTOPOOL.LOCAL
      DOMAIN: OCTOPOOL
      ADMIN_PASSWORD: Admin@123
      DNS_FORWARDER: 8.8.8.8
    volumes:
      - ./data:/var/lib/samba
    ports:
      - "53:53"
      - "53:53/udp"
      - "88:88"
      - "88:88/udp"
      - "135:135"
      - "137:137/udp"
      - "138:138/udp"
      - "139:139"
      - "389:389"
      - "389:389/udp"
      - "445:445"
      - "464:464"
      - "464:464/udp"
      - "636:636"
      - "3268:3268"
      - "3269:3269"
    restart: unless-stopped
  ```
  ### Chạy Samba DC
  - Up:
  ```
  docker compose up -d
  ```
  - Theo dõi log lần đầu
  ```
  docker logs -f samba-dc
  ```
  - Thấy log:
    - `Provisioning domain`
    - `DNS started`
    - `Samba AD DC started` 
