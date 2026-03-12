# ĐĂNG NHẬP CÁC DỊCH VỤ SỬ DỤNG LDAP SERVER CHUNG 1 TÀI KHOẢN 

## Tạo tài khoản từ KeyCloak
![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/1.png?raw=1)
![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/2.png?raw=1)
![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/3.png?raw=1)
![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/4.png?raw=1)
## Đăng nhập NextCloud
![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/5.png?raw=1)
![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/6.png?raw=1)
![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/7.png?raw=1)
![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/8.png?raw=1)

## Đăng nhập MailCow
- Login bằng pass cũ:
![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/9.png?raw=1)
- Login bằng pass mới
![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/10.png?raw=1)
## Kiểm tra trên LDAP - SambaDC
- Lệnh kiểm tra user đã tồn tại:
```
samba-tool user list
```
- Kết quả:
![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/11.png?raw=1)
## Đổi Password trên Keycloak
- Truy cập web:
```
https://auth.cloudnvt.km0.vn/realms/tunv_service/account
```
![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/12.png?raw=1)
![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/13.png?raw=1)
![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/14.png?raw=1)

- Đăng nhập lại
  - Pass cũ:
 ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/15.png?raw=1)
  - Pass mới
 ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/16.png?raw=1)

- Kết nối MailCow với NextCloud
  - Pass cũ:
 ![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/17.png?raw=1)
 ![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/18.png?raw=1)
  - Pass mới:
 ![Ảnh 19](https://github.com/hiiamtu16/InternReport/blob/20156b3b79340019889a99f2971a1db22d9f8a0b/Picture%20/Service/Samba%20-%20LDAP%20server%20%26%20MailCow%20-%20KeyCloak%20-%20Next%20Cloud/19.png?raw=1)
