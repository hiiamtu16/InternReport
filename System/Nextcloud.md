# NEXTCLOUD

## Khởi tạo Server
  - Clone máy Ubuntu (Xem lại CheckMK)
  - Cài NextCloud AIO trên Ubuntu
    - Cài Docker
      - sudo apt update
      - sudo apt install docker.io -y
      - sudo systemctl enable docker
      - sudo systemctl start docker
      - sudo usermod -aG docker $USER
    - Chạy Nextcloud AIO mastercontainer
      - sudo docker run -d --name nextcloud-aio-mastercontainer \
-p 80:80 -p 443:443 -p 8080:8080 \
--restart always \
-v nextcloud_aio_mastercontainer:/mnt/docker-aio-config \
-v /var/run/docker.sock:/var/run/docker.sock:ro \
nextcloud/all-in-one:latest
    - mở trình duyệt vào trang quản trị AIO: https://172.16.20.34:8080/ (Bắt buộc dùng https://ip-máy:8080) 
    


---

##
