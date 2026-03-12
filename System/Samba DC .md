# SAMBA DC

## Chuẩn bị Server
- Cập nhật hệ thống
```
sudo apt update
sudo apt upgrade -y
```
- Đặt hostname
```
sudo hostnamectl set-hostname tunv
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
        - 172.16.20.84/24
      routes:
        - to: default
          via: 172.16.20.1
      nameservers:
        addresses:
          - 127.0.0.1
          - 8.8.8.8
          
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
127.0.0.1 localhost
172.16.20.84 tunv.cloudnvt.km0.vn tunv
```

## Cài Package Samba DC
- 
```
sudo apt update
sudo apt install samba krb5-config winbind smbclient dnsutils -y
```
- Default realm nhập:
```
CLOUDNVT.KM0.VN
```
- Kerberos servers for your realm nhập:
```
tunv.cloudnvt.km0.vn
```
- Administrative server for your Kerberos realm nhập:
```
tunv.cloudnvt.km0.vn
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
    CLOUDNVT.KM0.VN
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
          default_realm = CLOUDNVT.KM0.VN
          dns_lookup_realm = false
          dns_lookup_kdc = true
  
  [realms]
  CLOUDNVT.KM0.VN = {
          default_domain = cloudnvt.km0.vn
          admin_server = tunv.cloudnvt.km0.vn
  }
  
  [domain_realm]
          .cloudnvt.km0.vn = CLOUDNVT.KM0.VN
          cloudnvt.km0.vn = CLOUDNVT.KM0.VN
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
    host -t SRV _ldap._tcp.tunv.cloudnvt.km0.vn
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
host -t A tunv.cloudnvt.km0.vn
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

## Tắt hiệu lực pass cũ sau khi đổi pass
- Mở file config
```
sudo nano /etc/samba/smb.conf
```
- Thêm vào phần `[global]`:
```
ntlm auth = yes
old password allowed period = 0
```
- Giá trị: 0  → không cho password cũ dùng
- Sau đó restart Samba:
```
systemctl restart samba-ad-dc
```
