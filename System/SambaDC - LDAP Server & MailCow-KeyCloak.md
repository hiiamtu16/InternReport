# ĐĂNG NHẬP CÁC DỊCH VỤ SỬ DỤNG LDAP SERVER CHUNG 1 TÀI KHOẢN 

## Tạo tài khoản từ KeyCloak
![Ảnh 1](?raw=1)
![Ảnh 2](?raw=1)
![Ảnh 3](?raw=1)
![Ảnh 4](?raw=1)
## Đăng nhập NextCloud
![Ảnh 5](?raw=1)
![Ảnh 6](?raw=1)
![Ảnh 7](?raw=1)
![Ảnh 8](?raw=1)

## Đăng nhập MailCow
- Login bằng pass cũ:
![Ảnh 9](?raw=1)
- Login bằng pass mới
![Ảnh 10](?raw=1)
## Kiểm tra trên LDAP - SambaDC
- Lệnh kiểm tra user đã tồn tại:
```
samba-tool user list
```
- Kết quả:
![Ảnh 11](?raw=1)
## Đổi Password trên Keycloak
- Truy cập web:
```
https://auth.cloudnvt.km0.vn/realms/tunv_service/account
```
![Ảnh 12](?raw=1)
![Ảnh 13](?raw=1)
![Ảnh 14](?raw=1)

- Đăng nhập lại
  - Pass cũ:
 ![Ảnh 15](?raw=1)
  - Pass mới
 ![Ảnh 16](?raw=1)

- Kết nối MailCow với NextCloud
  - Pass cũ:
 ![Ảnh 17](?raw=1)
  - Pass mới:
 ![Ảnh 18](?raw=1)
