# HAProxy

## 1. Cài đặt HAProxy
```
sudo apt update
sudo apt install -y haproxy

haproxy -v
```
## 2. Dừng HAProxy mặc định
```
sudo systemctl stop haproxy
```
## 3. Backup và mở file cấu hình
```
sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
sudo nano /etc/haproxy/haproxy.cfg
```
## 4. Cấu hình HAProxy HTTP + HTTPS cho Nextcloud và Keycloak
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
    timeout client  60s
    timeout server  60s

frontend http_in
    bind *:80
    redirect scheme https code 301 if !{ ssl_fc }

frontend https_in
    bind *:443 ssl \
        crt /etc/haproxy/certs/cloudnvt.km0.vn.pem \
        crt /etc/haproxy/certs/auth.cloudnvt.km0.vn.pem

    option forwardfor

    http-request set-header X-Forwarded-Proto https
    http-request set-header X-Forwarded-Port 443
    http-request set-header X-Forwarded-Host %[req.hdr(Host)]
    http-request set-header X-Forwarded-For %[src]

    acl host_nextcloud hdr(host) -i cloudnvt.km0.vn
    acl host_keycloak  hdr(host) -i auth.cloudnvt.km0.vn

    use_backend nextcloud_backend if host_nextcloud
    use_backend keycloak_backend  if host_keycloak
    default_backend nextcloud_backend

backend nextcloud_backend
    mode http
    server nextcloud 127.0.0.1:8080 check

backend keycloak_backend
    mode http
    option httpchk
    http-check send meth GET uri /realms/master ver HTTP/1.1 hdr Host auth.cloudnvt.km0.vn
    http-check expect status 200

    http-request set-header Host auth.cloudnvt.km0.vn
    http-request set-header X-Forwarded-Proto https
    http-request set-header X-Forwarded-Port 443
    http-request set-header X-Forwarded-Host auth.cloudnvt.km0.vn

    server keycloak 127.0.0.1:8081 check
```
## 5. Kiểm tra và restart HAProxy
```
haproxy -c -f /etc/haproxy/haproxy.cfg
sudo systemctl restart haproxy
sudo systemctl status haproxy
```
## 6. Kiểm tra hoạt động
```
curl -I https://cloudnvt.km0.vn
curl -I https://auth.cloudnvt.km0.vn
```
## 7. Ghi chú vận hành

- Sau khi cấu hình proxy lần đầu cần xoá cookie domain cũ
- Keycloak lỗi iframe chỉ do cookie cũ
- Luôn ép đầy đủ X-Forwarded headers
