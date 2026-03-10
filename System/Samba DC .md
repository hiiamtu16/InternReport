# SAMBA DC

## Chuẩn bị Server
- Cập nhật hệ thống
```
sudo apt update
sudo apt upgrade -y
```
- Đặt hostname
```
sudo hostnamectl set-hostname tunv-dc
```

## Cấu hình IP
- Mở file netplan
```
sudo nano /etc/netplan/50-cloud-init.yaml
```
- Đặt IP
```
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 172.16.20.81/24
      routes:
        - to: default
          via: 172.16.20.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```
- Thoát và Lưu cấu hình
- Apply
```
sudo netplan apply
```
- Kiểm tra IP
```
ip a
```

## Cập nhật /etc/hosts
- Mở file
```
sudo nano /etc/hosts
```
- Thêm dòng
```
172.16.20.81 tunv-dc.tunv.local tunv-dc
```

## Cài Package Samba DC
- 
```
sudo apt update
sudo apt install samba krb5-config winbind smbclient dnsutils -y
```
- Default realm nhập:
```
TUNV.LOCAL
```
- Kerberos servers for your realm nhập:
```
tunv-dc.tunv.local
```
- Administrative server for your Kerberos realm nhập:
```
tunv-dc.tunv.local
```

## Tạo Domain Controller
### Backup file cấu hình Samba
- Trước khi tạo domain cần xóa cấu hình cũ
```
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
```
### Tạo Active Directory Domain
- Chạy
```
sudo samba-tool domain provision
```
- Nhập các thông tin sau:
  - Realm:
    ```
    TUNV.LOCAL
    ```
  - Domain:
   ```
   TUNV
   ```
  - Server Role:
  ```
  dc
  ```
  - DNS backend:
   ```
   SAMBA_INTERNAL
   ```
  - DNS forwarder:
   ```
   8.8.8.8
   ```
  - Administrator password:
   ```
   123@123a
   ```
### Cấu hình Kerberos
- Copy cấu hình Kerberos mà Samba tạo:
```
sudo cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
```
### Tắt samba service cũ
```
sudo systemctl stop smbd nmbd winbind
sudo systemctl disable smbd nmbd winbind
```
### Bật Domain Controller
```
sudo systemctl unmask samba-ad-dc
sudo systemctl enable samba-ad-dc
sudo systemctl start samba-ad-dc
```
- Kiểm tra
```
systemctl status samba-ad-dc
```
- Nếu thấy active (running) là OK.

### Test Kerberos
- Cài gói:
```
sudo apt install krb5-user -y
```
- Sửa file `Kerberos` `/etc/krb5.conf`:
  - Mở file 
  ```
  sudo nano /etc/krb5.conf
  ```
  - Sửa toàn bộ nội dung thành:
  ```
  [libdefaults]
   default_realm = TUNV.LOCAL
   dns_lookup_realm = false
   dns_lookup_kdc = true
  
  [realms]
   TUNV.LOCAL = {
    default_domain = tunv.local
   }
  
  [domain_realm]
   .tunv.local = TUNV.LOCAL
   tunv.local = TUNV.LOCAL
  ```
- Sửa DNS listen IPv4
  - Mở file
    ```
    sudo nano /etc/samba/smb.conf
    ```
  - Trong phần `[global]` thêm:
   ```
   dns forwarder = 8.8.8.8
   interfaces = lo eth0
   bind interfaces only = yes
   ```
  - Restart Samba:
   ```
   sudo systemctl restart samba-ad-dc
   ```
  - Test lại DNS
    ```
    host -t SRV _ldap._tcp.tunv.local
    ```
- Chạy lệnh
```
kinit administrator
```
- Nhập password admin
- kiểm tra:
```
klist
```
### Test DNS Domain
```
host -t A tunv.local
```

### Đổi DNS của server về chính nó
- Mở file cấu hình Netplan
```
sudo nano /etc/netplan/50-cloud-init.yaml
```
- Sửa:
```
nameservers:
  addresses:
    - 127.0.0.1
```
- Apply cấu hình
```
sudo netplan apply
```
### Tạo user test
- Tạo
```
sudo samba-tool user create tunv1
```
- Password
```
123@123a
```

## Join Domain
- Trỏ DNS về DC
- Join domain bằng tài khoản administrator
- Login bằng tài khoản người dùng
- Kết quả:
  ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/f6b89423446c5b2b2961e8c03cfd20afcc629c9d/Picture%20/Service/SambaDC/1.png?raw=1)
