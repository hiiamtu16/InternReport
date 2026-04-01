#  GITLAB

## Set up VM Ubuntu và Docker
### Cài Docker
```
sudo apt update
sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
```

### Tạo thư mục lưu dữ liệu
```
sudo mkdir -p /srv/gitlab/config
sudo mkdir -p /srv/gitlab/logs
sudo mkdir -p /srv/gitlab/data
```

### Tạo Docker compose
- Tạo file:
```
nano docker-compose.yml
```
- Nội dung:
```
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    hostname: 172.16.20.10
    restart: always
    shm_size: 512m

    ports:
      - "8080:80"
      - "8443:443"
      - "2222:22"

    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://172.16.20.10:8080'
        gitlab_rails['gitlab_shell_ssh_port'] = 2222

        # tối ưu nhẹ cho RAM 6GB
        puma['worker_processes'] = 2
        sidekiq['concurrency'] = 10

    volumes:
      - /srv/gitlab/config:/etc/gitlab
      - /srv/gitlab/logs:/var/log/gitlab
      - /srv/gitlab/data:/var/opt/gitlab
```
### Chạy Gitlab
```
sudo docker-compose up -d
```

### Accept iptables
- kiểm tra:
```
sudo iptables -L -n -v
```
- accept tất cả:
```
sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT
```
- Kiểm ra Docker:
```
sudo iptables -S
```
- 

### Truy cập hệ thống
- Mở trình duyệt: (ip máy port 8080)
```
http://172.16.20.112:8080 
```
- Tạo tài khoản `root`:
```
sudo docker exec -it gitlab gitlab-rails console
```
- Nhập lần lượt: (tự thay đổi thông tin mật khẩu)
```
u = User.find_by_username('root')
u.password = '123@123a'
u.password_confirmation = '123@123a'
u.password_automatically_set = false
u.save!
exit
```
- Đăng nhập bằng tài khoản admin:
![Ảnh 1](?raw=1)
![Ảnh 2](?raw=1)
- Tạo tài khoản user
![Ảnh 3](?raw=1)
![Ảnh 4](?raw=1)
- Admin duyệt
![Ảnh 5](?raw=1)
- User login bằng username
![Ảnh 6](?raw=1)
![Ảnh 7](?raw=1)
- User login bằng email
![Ảnh 8](?raw=1)
![Ảnh 7](?raw=1)

## Lưu Logs hệ thống (GitLab Docker) sang 1 ổ mới
### Add thêm ổ trên phần cứng
- Bước 1: format ổ mới
```
sudo mkfs.ext4 /dev/sdb
```
- Bước 2: tạo mount point
```
sudo mkdir -p /mnt/gitlab-data
```
- Bước 3: mount thử
```
sudo mount /dev/sdb /mnt/gitlab-data
```
  - Check:
  ```
  df -h
  ```
  - phải thấy:
  ```
  /dev/sdb  -> /mnt/gitlab-data
  ```
- Bước 4: stop GitLab
```
sudo docker-compose down
```
- Bước 5: copy dữ liệu sang ổ mới
```
sudo rsync -av /srv/gitlab/ /mnt/gitlab-data/
```
- Bước 6: backup thư mục cũ (phòng lỗi)
```
sudo mv /srv/gitlab /srv/gitlab_backup
```
- Bước 7: mount lại vào đúng chỗ
```
sudo mkdir -p /srv/gitlab
sudo mount /dev/sdb /srv/gitlab
```
- Bước 8: restore data vào mount mới
```
sudo rsync -av /mnt/gitlab-data/ /srv/gitlab/
```
- Bước 9: set quyền
```
sudo chown -R root:root /srv/gitlab
```
- Bước 10: bật lại GitLab
```
sudo docker-compose up -d
```
 - Kiểm tra
```
df -h
```
 - `/srv/gitlab` phải nằm trên `/dev/sdb`

### Mount tự động khi reboot
- Lấy UUID:
```
sudo blkid /dev/sdb
```
- Sửa:
```
sudo nano /etc/fstab
```
- Thêm dòng (thay xxx-xxx bằng UUID vừa lấy):
```
UUID=xxxx-xxxx  /srv/gitlab  ext4  defaults  0  2
```
- 

