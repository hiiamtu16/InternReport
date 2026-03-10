# LIÊN KẾT SAMBA-DC VỚI KEYCLOAK QUA LDAP 

## Kiểm tra trên DC
### Kiểm tra LDAP
  - Install `ldapsearch`
  ```
  apt install ldap-utils
  ```
  - Test trên DC:
  ```
  ldapsearch -x -H ldap://127.0.0.1 -b "dc=tunv,dc=local"
  ```
  - Test User:
  ```
  ldapsearch -x -H ldap://127.0.0.1 \
  -b "dc=tunv,dc=local" \
  "(sAMAccountName=tunv1)"
  ```
### Bind account cho LDAP
- Tạo account cho Keycloak sử dụng để liên kết
```
samba-tool user create ldapbind
```
- Password
```
123@123a
```
- Tắt StartTLS
  - Mở Samba config:
   ```
   sudo nano /etc/samba/smb.conf
   ```
  - Trong phần `[global]` thêm dòng:
   ```
   ldap server require strong auth = no
   ```
  - Lưu lại rồi restart:
   ```
   sudo systemctl restart samba-ad-dc
   ```
  - Sau đó kiểm tra lại ngay trên DC:
   ```
   ldapsearch -x -H ldap://127.0.0.1 \
  -D "cn=ldapbind,cn=Users,dc=tunv,dc=local" \
  -W \
  -b "dc=tunv,dc=local" "(sAMAccountName=ldapbind)" dn
   ```
  - Lệnh này trả về DN của `ldapbind` là OK

## Cấu hình trên KeyCloak
![Ảnh 1](?raw=1)
![Ảnh 2](?raw=1)
![Ảnh 3](?raw=1)
![Ảnh 4](?raw=1)
![Ảnh 5](?raw=1)

