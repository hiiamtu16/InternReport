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
  ### Cài Gparted
   * Click chuột phải vào màn hình chọn Open in Terminal
   * Update: sudo apt update
   * Cài Gparted: sudo apt install gparted  (Y - Enter)
   * Mở Gparted: gparted
   * Bật phần ổ cứng đang unallocated và tách thành 2 ổ, 1 để lưu dữ liệu, 1 để làm SWAP
   ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/df15b336ecc36861ce19aaacba2ff73ce6984b0d/Picture%20/Operating%20System/Ubuntu22.04/8.png?raw=1)
   ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/9.png?raw=1)
  ### Phân ổ
   * Kiểm tra phân vùng ổ: lsblk -f
   * sda2 làm ổ lưu data
     - format ổ: sudo mkfs.ext4 /dev/sda2
     - tạo thư mục mount point: sudo mkdir /data (có thể đặt tên khác data)
     - Mount phân vùng dữ liệu: sudo mount /dev/sda2 /data
     - Thêm phân vùng vào /etc/fstab để mount tự động:
       + Mở file /etc/fstab: sudo nano /etc/fstab
       + Thêm dòng sau vào cuối file: /dev/sda2   /data   ext4    defaults    0   2
       + Lưu file và thoát (Ctrl+X, sau đó nhấn Y và Enter để lưu thay đổi)
   * sda3 làm SWAP:
     - format ổ: sudo mkswap /dev/sda3
     - kích hoạt: sudo swapon /dev/sda3
     - thêm phân vùng swap vào fstab:
       + Mở file /etc/fstab: sudo nano /etc/fstab
       + Thêm vào cuối file: /dev/sda3   none    swap    sw    0   0
       + Lưu file và thoát (Ctrl+X, sau đó nhấn Y và Enter để lưu thay đổi)
    * Kiểm tra:
     - swapon -s
     - df -h 
      ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/10.png?raw=1)
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

 ## Đặt lại mật khẩu root:
  * Bật máy, bấm restart đợi boost rồi bấm liền tổ hợp (Shift-Esc)
    ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/10.png?raw=1)
    ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/11.png?raw=1)
    ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/12.png?raw=1)
    ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/13.png?raw=1)
  * Tạo root password:
    ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/14.png?raw=1)
  * Đổi password:
    ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/50087bffd804b77381b62d4b882f7b3df87232fc/Picture%20/Operating%20System/Ubuntu22.04/15.png?raw=1)
  * Enter, gõ "exit" chọn "resume" -> ok để chạy lại
# CentOS7

# AlmaLinux
## Phân vùng ổ cứng
  * Cài đặt Gparted
    - Bật EPEL repo: sudo dnf install epel-release -y
    - Cài Gparted: sudo dnf install gparted -y
    - Mở Gparted: gparted
  * 
