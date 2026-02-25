# SAMBA DC

## Cài Samba DC docker
  ### Cài Docker và Docker Compose
  - Cài Docker
  ```
  sudo apt update
  sudo apt install ca-certificates curl gnupg -y
  
  sudo install -m 0755 -d /etc/apt/keyrings
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
  sudo chmod a+r /etc/apt/keyrings/docker.gpg
  
  echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
  sudo apt update
  sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
  ```
  - Active
  ```
  sudo systemctl enable --now docker
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
