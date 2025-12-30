# FILE & OFFICE

## Mở File dung lượng lớn trên NextCloud
  


---
## Mở, Chỉnh sửa file Office trên NextCloud
  - Chuẩn bị SSL cert:
    - Kiểm tra cert cho office.cloudnvt.km0.vn: echo | openssl s_client -servername office.cloudnvt.km0.vn -connect 210.245.58.99:443 2>/dev/null | openssl x509 -noout -text | grep -A1 "Subject Alternative Name"
    - Tắt HAProxy tạm: sudo systemctl stop haproxy
    - Xin cert: certbot certonly --standalone -d office.cloudnvt.km0.vn
    - Kiểm tra cert:
      - ls -l /etc/letsencrypt/live/office.cloudnvt.km0.vn/
      - openssl x509 -in /etc/letsencrypt/live/office.cloudnvt.km0.vn/fullchain.pem -noout -dates
    - Ghép cert cho domain:
      ```
      sudo cat \
      /etc/letsencrypt/live/office.cloudnvt.km0.vn/fullchain.pem \
      /etc/letsencrypt/live/office.cloudnvt.km0.vn/privkey.pem \
      > /etc/haproxy/certs/office.cloudnvt.km0.vn.pem
      ```
    - Tạo file PEM (cert) cho HAProxy:
      ```
      cat /etc/letsencrypt/live/auth.cloudnvt.km0.vn/fullchain.pem \
      /etc/letsencrypt/live/auth.cloudnvt.km0.vn/privkey.pem \
      > /etc/haproxy/certs/cloudnvt.km0.vn.pem

       chmod 600 /etc/ssl/private/cloudnvt.pem
      ```
  - Chạy Collabora chỉ listen localhost:
    ```
    sudo docker run -d \
      --name collabora \
      -p 127.0.0.1:9980:9980 \
      -e "domain=cloudnvt\\.km0\\.vn" \
      -e "username=admin" \
      -e "password=StrongPasswordHere" \
      --restart always \
      collabora/code
    ```
  - Cấu hình route HAproxy
    - Mở file cấu hình: sudo nano /etc/haproxy/haproxy.cfg
    - Thêm ACL:
      - acl host_office hdr(host) -i office.cloudnvt.km0.vn
      - use_backend bk_collabora if host_office
    - Thêm backend Collabora ở cuối file:
     ```
    backend bk_collabora
      mode http
      option httpchk GET /hosting/discovery
      http-check expect status 200
    
      http-request set-header X-Forwarded-Proto https
      http-request set-header X-Forwarded-Ssl on
    
      server collabora1 127.0.0.1:9980 ssl verify none
     ```
    - Check và reload HAProxy: sudo haproxy -c -f /etc/haproxy/haproxy.cfg
    - Start lại HAProxy: sudo systemctl start haproxy
    - Reload lại HAProxy: sudo systemctl reload haproxy

  Cấu hình trên NextCloud:
  ![Ảnh 1](?raw=1)
  Test: 
  ![Ảnh 2](?raw=1)


  
