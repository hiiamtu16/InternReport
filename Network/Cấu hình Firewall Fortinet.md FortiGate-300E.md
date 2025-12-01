# Cấu hình Firewall Fortinet FortiGate-300E

## 1. Kết nối

### 1.1. Kết nối với cổng MGMT
- Dùng dây **RJ45 chuẩn** cắm máy tính vào cổng **MGMT** của Firewall.

### 1.2. Kiểm tra IP máy tính
- Mở CMD và nhập:  ipconfig
- Xem **Default Gateway**, đó chính là IP để truy cập Web GUI:
- Ví dụ: `http://192.168.1.99`

![Check IP](https://github.com/hiiamtu16/InternReport/blob/3c10797db448de5b8658e702e81872fe4fd88dbf/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/CheckIP.png?raw=1)

### 1.3. Đăng nhập Web GUI
- Username: **admin**  
- Password: **hpa#2025**

![Login Web](https://github.com/hiiamtu16/InternReport/blob/043bdcd189c99fe0b11615a7d470e4fc421be96e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/login_web.png?raw=1)

---

## 2. Tạo Interface

### 2.1. Tạo WAN Interface
- Chọn **cổng kết nối** từ modem ISP sang Firewall.

![Chọn Port](https://github.com/hiiamtu16/InternReport/blob/265780e8ccbfa4c8fe45a07d777526fa013accef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/choosePort.png?raw=1)

- Chọn **Edit** và thiết lập thông số:
- Đặt **Alias**
- Role: **WAN**
- Addressing mode: **PPPoE**
- Nhập Username & Password PPPoE  
  - Nếu là **FPT** → gọi **1900 6600 nhánh 2** yêu cầu reset MAC (cung cấp MST + tên tài khoản)
- Bật các dịch vụ quản trị: ping / http / https / ssh …

![Edit Interface 1](https://github.com/hiiamtu16/InternReport/blob/04c750708a7876a86d4aea5ff2ac1f3d7b1ac2a5/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Edit%20Interface%201.png?raw=1)

![Edit Interface 2](https://github.com/hiiamtu16/InternReport/blob/04c750708a7876a86d4aea5ff2ac1f3d7b1ac2a5/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Edit%20Interface%202.png?raw=1)

---

### 2.2. Tạo Loopback Interface

![Create Loopback](https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/create%20loopback%20interface.png?raw=1)

- Điền thông tin theo hình:

![Loopback Details](https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/interface%20loopback.png?raw=1)

---

## 3. Tạo SD-WAN

### 3.1. Tạo SD-WAN Zones

![SD-WAN Zones](https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Create%20SD-WAN%20Zones.png?raw=1)

![New SD-WAN Zone](https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/New%20Zones.png?raw=1)

---

### 3.2. Thêm SD-WAN Member

![Create SD-WAN Member](https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Create%20SD-WAN%20Member.png?raw=1)

![New Member](https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/New%20Member.png?raw=1)

---

### 3.3. Tạo Performance SLAs

![Create SLA](https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Create%20SLAs.png?raw=1)

![New SLA](https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/New%20SLA.png?raw=1)

---

### 3.4. Tạo SD-WAN Rule

![Create Rule](https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ADnh%20Firewall%20Fortinet%20FortiGate-300E/create%20rule.png?raw=1)

![Priority Rule](https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ADnh%20Firewall%20Fortinet%20FortiGate-300E/priority%20rule.png?raw=1)

![Priority Rule 2](https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ADnh%20Firewall%20Fortinet%20FortiGate-300E/priority%20rule%202.png?raw=1)

---

## 4. Tạo Firewall Policy

⚠ **Lưu ý:**  
Mỗi Firewall Policy chỉ áp dụng cho **một IP Loopback**.  
Nếu muốn áp dụng **cả dải Loopback /29** → cần tạo nhiều Policy tương ứng.

![Policy List](https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ADnh%20Firewall%20Fortinet%20FortiGate-300E/Firewall%20Policy.png?raw=1)

![Edit Policy](https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ADnh%20Firewall%20Fortinet%20FortiGate-300E/edit%20policy.png?raw=1)

![Edit Policy 2](https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/edit%20policy%202.png?raw=1)



