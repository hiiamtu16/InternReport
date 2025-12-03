# Hướng dẫn cấu hình Router TP-Link Omada ER8411

## 1. Kết nối với Omada ER8411

### • Kết nối vật lý
- Cắm PC vào **LAN Port 11** (Management Port của hệ thống)
- PC sẽ nhận IP từ router → dùng địa chỉ Gateway để truy cập Web

![Lấy IP](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/CheckIP.png?raw=1)

### • Đăng nhập trang quản trị
![Login Web](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Login%20Web.png?raw=1)

---

## 2. Cấu hình WAN

### • Chọn cổng WAN dùng để kết nối Internet
![Select WAN Port](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Select%20WAN%20Port.png?raw=1)

### • Cấu hình PPPoE trên WAN
![Setting WAN](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Setting%20WAN.png?raw=1)

---

## 3. Cấu hình LAN

### • LAN quản lý Router
![Edit Mana](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Edit%20Mana.png?raw=1)

![Setting Mana](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Setting%20MANA%20LAN.png?raw=1)

### • LAN cấp DHCP cho thiết bị
![Edit LAN DHCP](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Setting%20new%20LAN%20DHCP.png?raw=1)

---

## 4. Cấu hình VLAN

### • Tạo VLAN mới
![Edit VLAN](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Edit%20VLAN.png?raw=1)

![Edit VLAN 2](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Edit%20VLAN%202.png?raw=1)

### • Gán cổng vật lý vào VLAN
![Edit VLAN Port](https://github.com/hiiamtu16/InternReport/blob/dc0344ce1919a712360b663e7d0ab872971a8ea2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/Edit%20Port%20VLAN.png?raw)

---

## 5. Kiểm tra kết quả
Ping và tracert ra ngoài Internet
![Ping & tracert](https://github.com/hiiamtu16/InternReport/blob/b82f6e5c905001d111e3869ff2d49b0faa1a5b77/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Router%20Omada%20ER8411/ping%20%26%20tracert.png?raw=1)
