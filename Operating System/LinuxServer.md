# CentOS7
## Cài đặt 
 * Thông số cài đặt
 ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/1.png?raw=1)
 ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/2.png?raw=1)
 * Phân vùng ổ cứng thủ công
 ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/3.png?raw=1)
 ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/4.png?raw=1)
 - Tối thiểu 20GB cho ổ Root
 - phân vùng ổ swap, boot, var, home
 ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/5.png?raw=1)
 * Tạo User Admin
 ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/6.png?raw=1)
 * Tạo pass root
 ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/7.png?raw=1)
## Cấu hình IP
  * kiểm tra cấu hình mạng: ip a
  * Bật kết nối: sudo ifup **eno16777736**(tên card mạng, có thể thay đổi)
  * Mở file cấu hình của interface cần cấu hình IP tĩnh: sudo nano /etc/sysconfig/network-scripts/ifcfg-**eno16777736**(tên card mạng)
  * File cấu hình:
    - TYPE="Ethernet"
    - BOOTPROTO="static"
    - NAME="ens33"        # Thay "ens33" bằng tên interface 
    - DEVICE="ens33"      # Thay "ens33" bằng tên interface 
    - ONBOOT="yes"        # Giao diện sẽ được kích hoạt khi khởi động
    - IPADDR="192.168.16.100"   
    - NETMASK="255.255.255.0"   
    - GATEWAY="192.168.16.1"     
    - DNS1="8.8.8.8"          
    - DNS2="8.8.4.4"    
  * Restart dịch vụ mạng: sudo systemctl restart network
  * Bật dịch vụ mạng: sudo systemctl enable network

## Reset password
 * Restart máy, bấm tổ hợp (Shift + Esc) vào boost, Bấm E
 * Chuyển con trỏ xuống cuối dòng root, thêm: rd.break
 ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/1.png?raw=1)
 * Vào mode và đổi Pass:
 ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/11.png?raw=1)

---

# AlmaLinux

---

# RockyLinux 9.
