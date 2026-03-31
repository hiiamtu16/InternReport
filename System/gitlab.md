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
u.password = 'MatKhauMoi123!'
u.password_confirmation = 'MatKhauMoi123!'
u.password_automatically_set = false
u.save!
exit
```

- 

