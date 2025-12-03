CẤU HÌNH ROUTER MIKROTIK CHO PC LẤY IP TRỰC TIẾP 

1. Kết nối PC với MikroTik
  * Kết nối PC với cổng ETH/BOOT trên router bằng dây RJ45-RJ45
  * Dùng phần mềm WinBox đăng nhập qua MAC bằng username và Password
    - Username: admin
    - Password: E$rmDB4%B
      ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/1.png?raw=1)
2. Cấu hình
  * Cấu hình nhanh:
    ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/2.png?raw=1)  
  * Cấu hình Interface:
    - Đăt tên cổng vật lý để quản lý hướng đường truyền
      ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/3.png?raw=1)
    - Tạo Vlan gắn trên cổng vật lý
      ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/4.png?raw=1)
  * Tạo Bridge
    - Tạo Bridge để gắn Cổng vật lý và Vlan, chuyển cổng vật lý từ mode Trunk (mặc định) sang mode Access
       ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/5.png?raw=1)
    - Gán cổng và Vlan vào Bridge
      ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/6.png?raw=1)
    - Kiểm tra thông số Bridge
      ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/7.png?raw=1)
  * Cấu hình IP và DHCP
    - Cấu hình IP Address trên Bridge
      ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/8.png?raw=1)
    - Tạo Pool DHCP
      ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/9.png?raw=1)
    - Cấu hình DHCP Server trên Bridge
      ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/10.png?raw=1)
  
3. Kết quả
  * Lấy IP từ DHCP và Ping đến Fw
    ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/11.png?raw=1)
  * Ping và Tracert ra Internet
    ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/12.png?raw=1)
