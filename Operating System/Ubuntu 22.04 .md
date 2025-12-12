# Ubuntu
## Cài đặt Ubuntu
  * Tạo máy ảo trên VMware Workstation: RAM tối thiểu 2GB, Disk tối thiểu 30GB
  * Chọn bộ cài Ubuntu 22.
  * Setup theo các bước
  ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/1.png?raw=1)
  ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/2.png?raw=1)
  ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/3.png?raw=1)
  ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/4.png?raw=1)
  ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/5.png?raw=1)
  ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/6.png?raw=1)
  ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/7.png?raw=1)

## Phân vùng ổ cứng thủ công
  
## Đặt IP tĩnh:
  - xem card mạng: ip a
  - xem file cấu hình netplan: ls /etc/netplan      ( có dạng: 00-installer-config.yaml)
  - mở file netplan để cấu hình: sudo nano /etc/netplan/00-installer-config.yaml
  - file cấu hình IP Tĩnh:
    network:
    version: 2
    renderer: networkd
    ethernets:
      ens33:
        dhcp4: no
        addresses:
          - 192.168.1.50/24
        gateway4: 192.168.1.1
        nameservers:
          addresses:
            - 8.8.8.8
            - 1.1.1.1
  - File cấu hình lấy IP từ DHCP:
    network:
    version: 2
    renderer: networkd
    ethernets:
      ens33:
        dhcp4: true
        dhcp6: true (hoặc false nếu không lấy IPv6)
  - Lưu file cấu hình: Ctrl+O - Enter - Ctrl+X - Y - Enter
  - Áp dụng cấu hình: sudo netplan apply
  - debug lỗi: sudo netplan --debug apply

# CentOS7

# AlmaLinux
## Phân vùng ổ cứng
  * Cài đặt Gparted
    - Bật EPEL repo: sudo dnf install epel-release -y
    - Cài Gparted: sudo dnf install gparted -y
    - Mở Gparted: gparted
  * 
