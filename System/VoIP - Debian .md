# VoIP

## Clone VoIP Template VM 8004
- TK Login:
```
root
```
```
VuongHuy@84
```
- Bật card mạng:
  - Check card mạng:
  ```
  ip a
  ```
  - Bật card mạng (VD card tên ens18, k thì đổi tên):
  ```
  ip link set ens18 up
  dhclient ens18
  ip a
  ```
  - Mở file
  ```
  nano /etc/network/interfaces
  ```
  - Chỉnh cấu hình
  ```
  auto lo
  iface lo inet loopback
  
  auto ens18
  allow-hotplug ens18
  iface ens18 inet dhcp
  ```
  - Restart network
  ```
  systemctl restart networking
  ```
- Resize ổ cứng (phần thêm vào là sda3)
  - Kiểm tra kiểu bảng phân vùng
   ```
   fdisk -l /dev/sda
   ```
  - Tạo phân vùng mới
   ```
   fdisk /dev/sda
   ```
  - Trong fdisk gõ lần lượt:
   ```
   n
   p
   3
   
   
   w
   ```
  - Sau đó format:
   ```
   mkfs.ext4 /dev/sda3
   ```
  - Tạo thư mục mount:
   ```
   mkdir -p /data
   mount /dev/sda3 /data
   df -h
   ```
  - Lấy UUID để mount tự động khi reboot:
   ```
   blkid /dev/sda3
   ```
  - Lấy UUID rồi thêm vào
    ```
    nano /etc/fstab
    ```
  - Thêm dòng (thay xxx-xxx bằng UUID vừa lấy:
   ```
   UUID=xxxxx-xxxxx /data ext4 defaults 0 2
   ```
  - Kiểm tra
   ```
   systemctl daemon-reload
   mount -a
   ```
- Reboot
```
reboot
```

## Cấu hình hệ thống
- Truy cập web
```
172.16.5.33
```
- Tạo tài khoản `admin`, pass `123@123a`

### Cấu hình Extention
- Tạo Extention
![Ảnh 1](?raw=1)
![Ảnh 2](?raw=1)
![Ảnh 3](?raw=1)
![Ảnh 4](?raw=1)
![Ảnh 5](?raw=1)

### Cấu hinh Class of Service
![Ảnh 6](?raw=1)

###


## Cấu hình OpenVox
### Truy cập web 
- Reset về mặc định thiết bị OpenVOX
- Cắm dây vào Port 1 nối với PC
- Đặt IP tĩnh PC ` 172.16.98.10`
- Truy cập web `172.16.98.1`
- Tài khoản mặc định: `admin` / `admin`

### Cáu hình cơ bản
- Đổi pass mặc định: `admin` / `Hpa@2026`
- Đổi ngôn ngữ `English`
- Chọn múi giờ 7+
- Chuyển sang Static IP, Đặt ip cùng dải với VM HAPBX: `172.16.5.245`
- Cấu hình Endpoint SIP
![Ảnh 7](?raw=1)
- 
