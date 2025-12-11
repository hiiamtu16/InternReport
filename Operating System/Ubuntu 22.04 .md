# Ubuntu
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
