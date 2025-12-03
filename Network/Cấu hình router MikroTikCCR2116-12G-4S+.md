# Cấu hình Router MikroTik cho PC lấy IP trực tiếp

## 1. Kết nối PC với MikroTik

* Kết nối PC với cổng **ETH/BOOT** trên router bằng dây **RJ45–RJ45**  
* Dùng phần mềm **WinBox** để đăng nhập qua MAC  

  Username: admin
  Password: Password  

![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/1.png?raw=1)

---

## 2. Cấu hình

### 2.1. Cấu hình nhanh

![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/2.png?raw=1)

---

### 2.2. Cấu hình Interface

* Đặt tên cổng vật lý để dễ quản lý luồng truyền  

![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/3.png?raw=1)

* Tạo VLAN gắn trên cổng vật lý  

![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/4.png?raw=1)

---

### 2.3. Tạo Bridge

* Tạo Bridge để gắn cổng vật lý và VLAN, chuyển cổng vật lý từ chế độ **Trunk (mặc định)** sang **Access**

![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/5.png?raw=1)

* Gán cổng và VLAN vào Bridge  

![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/6.png?raw=1)

* Kiểm tra thông số Bridge  

![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/7.png?raw=1)

---

### 2.4. Cấu hình IP và DHCP

* Cấu hình **IP Address** trên Bridge  

![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/8.png?raw=1)

* Tạo **DHCP Pool**

![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/9.png?raw=1)

* Cấu hình **DHCP Server** trên Bridge  

![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/10.png?raw=1)

---

## 3. Kết quả

* PC lấy IP từ DHCP và Ping được Firewall  

![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/11.png?raw=1)

* Ping và Tracert ra Internet  

![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/cd63345c6578403f53ab2983e479ea13556f8827/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20router%20Mikrotik%20CCR2116-12G-4S%2B/12.png?raw=1)
  
