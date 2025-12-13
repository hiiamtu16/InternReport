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
  * Kiểm tra cấu hình IP
     ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/8f3e632d477b0dfed33fc2a49fa5b91ca994bb14/Picture%20/Operating%20System/CentOS7/14.png?raw=1)

## Reset password
 * Restart máy, bấm tổ hợp (Shift + Esc) vào boost, Bấm E
 * Chuyển con trỏ xuống cuối dòng root, thêm: rd.break
 ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/10.png?raw=1)
 * Vào mode và đổi Pass:
 ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/12ca79be2eeee7ef50abfeb2090b4f55c1c9362e/Picture%20/Operating%20System/CentOS7/11.png?raw=1)

---

# AlmaLinux
## Cài đặt
* Phân vùng ổ cứng thủ công
 ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/fb9c3071de2fd8e59605f78e3edf4ab8a7470ba6/Picture%20/Operating%20System/AlmaLinux10/1.png?raw=1)
 ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/fb9c3071de2fd8e59605f78e3edf4ab8a7470ba6/Picture%20/Operating%20System/AlmaLinux10/2.png?raw=1)
 * Tạo User Admin
 ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/fb9c3071de2fd8e59605f78e3edf4ab8a7470ba6/Picture%20/Operating%20System/AlmaLinux10/4.png?raw=1)
 * Tạo pass root
 ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/fb9c3071de2fd8e59605f78e3edf4ab8a7470ba6/Picture%20/Operating%20System/AlmaLinux10/3.png?raw=1)
## Cấu hình IP
 * Kiểm tra cấu hình mạng: ip a
 * Kiểm tra trạng thái Interface: nmcli device status
 * Cấu hình IP tĩnh cho Interface ens160: sudo nmcli con mod ens160 ipv4.method manual ipv4.addresses 192.168.16.10/24 ipv4.gateway 192.168.16.1 ipv4.dns "8.8.8.8 8.8.4.4"
 * Kích hoạt lại kết nối: sudo nmcli con up ens160
 * Kiểm tra IP sau cấu hình
 ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/fb9c3071de2fd8e59605f78e3edf4ab8a7470ba6/Picture%20/Operating%20System/AlmaLinux10/8.png?raw=1)
## Reset password Root và Admin
 * Restart máy, bấm tổ hợp (Shift + Esc) vào boost, Bấm E
 ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/fb9c3071de2fd8e59605f78e3edf4ab8a7470ba6/Picture%20/Operating%20System/AlmaLinux10/5.png?raw=1)
 * Chuyển con trỏ xuống thay ro-->rw, thêm: rd.break
 ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/fb9c3071de2fd8e59605f78e3edf4ab8a7470ba6/Picture%20/Operating%20System/AlmaLinux10/6.png?raw=1)
 * Vào mode và đổi Pass:
 ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/fb9c3071de2fd8e59605f78e3edf4ab8a7470ba6/Picture%20/Operating%20System/AlmaLinux10/7.png?raw=1)

---

# RockyLinux 9.
## Cài đặt
 * Thông số cài đặt
 ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/6d38f4f51ae42b58a98364746b9644aac4155fee/Picture%20/Operating%20System/Rocky%20Linux%209.7/1.png?raw=1)
 * Phân vùng ổ cứng thủ công
 ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/6d38f4f51ae42b58a98364746b9644aac4155fee/Picture%20/Operating%20System/Rocky%20Linux%209.7/2.png?raw=1)
 - Tối thiểu 20GB cho ổ Root
 - phân vùng ổ swap, boot, var, home
 * Tạo User Admin
 ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/6d38f4f51ae42b58a98364746b9644aac4155fee/Picture%20/Operating%20System/Rocky%20Linux%209.7/4.png?raw=1)
 * Tạo pass root
 ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/6d38f4f51ae42b58a98364746b9644aac4155fee/Picture%20/Operating%20System/Rocky%20Linux%209.7/3.png?raw=1)
## Cấu hình IP
 * Kiểm tra cấu hình mạng: ip a
 * Kiểm tra trạng thái Interface: nmcli device status
 * Cấu hình IP tĩnh cho Interface ens160: sudo nmcli con mod ens160 ipv4.method manual ipv4.addresses 192.168.16.10/24 ipv4.gateway 192.168.16.1 ipv4.dns "8.8.8.8 8.8.4.4"
 * Kích hoạt lại kết nối: sudo nmcli con up ens160
 * Kiểm tra cấu hình IP
    ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/8f3e632d477b0dfed33fc2a49fa5b91ca994bb14/Picture%20/Operating%20System/Rocky%20Linux%209.7/9.png?raw=1)
## Reset password
 * Restart máy, bấm tổ hợp (Shift + Esc) vào boost, Bấm E
 ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/6d38f4f51ae42b58a98364746b9644aac4155fee/Picture%20/Operating%20System/Rocky%20Linux%209.7/5.png?raw=1)
 * Chuyển con trỏ xuống cuối dòng root, thêm: rd.break
 ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/6d38f4f51ae42b58a98364746b9644aac4155fee/Picture%20/Operating%20System/Rocky%20Linux%209.7/6.png?raw=1)
 * Vào mode và đổi Pass:
 ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/6d38f4f51ae42b58a98364746b9644aac4155fee/Picture%20/Operating%20System/Rocky%20Linux%209.7/8.png?raw=1)
