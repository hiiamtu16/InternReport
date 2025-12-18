# PFSENSE

## Cài đặt pfSense
  * Cấu hình máy VMware cài PfSense
    - Khởi tạo máy
    ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/1.png?raw=1)
    - Cấu hình máy 2 card mạng:
    ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/2.png?raw=1)
    ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/3.png?raw=1)
    - Add file OS
    ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/4.png?raw=1)
    - Chạy cài đặt
    ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/5.png?raw=1)
  * Tạo 1 PC để cấu hình Web GUI
    - Card mạng:
    ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/6.png?raw=1)
    - Truy cập Web GUI;
    ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/7.png?raw=1)
  * Cấu hình lần đầu
    ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/8.png?raw=1)
    ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/9.png?raw=1)
    ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/10.png?raw=1)
    ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/11.png?raw=1)
    ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/12.png?raw=1)
    
---

## NAT Local Internet
  ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/13.png?raw=1)

---

## Firewall Rule
  * Xoá các rule mặc định khởi tạo sẵn
    ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/14.png?raw=1)
  * Add rule LAN
    ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/15.png?raw=1)
  * Chưa có rule WAN
    ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/16.png?raw=1)
    ![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/17.png?raw=1)
  * Add rule WAN
    ![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/18.png?raw=1)
    ![Ảnh 19](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/19.png?raw=1)

---

## DHCP
  ![Ảnh 20](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/20.png?raw=1)
  ![Ảnh 21](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/21.png?raw=1)
  ![Ảnh 22](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/22.png?raw=1)

---

## OpenVPN 
  * Tạo CA
    ![Ảnh 23](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/23.png?raw=1)
    ![Ảnh 24](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/24.png?raw=1)
  * Tạo Cert cho Server OpenVPN
    ![Ảnh 25.1](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/25.1.png?raw=1)
    ![Ảnh 25.2](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/25.2.png?raw=1)
  * Tạo User
    ![Ảnh 26](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/26.png?raw=1)
    ![Ảnh 27](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/27.png?raw=1)
    ![Ảnh 28](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/28.png?raw=1)
  * Tạo Server OpenVPN
    ![Ảnh 29](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/29.png?raw=1)
    ![Ảnh 30](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/30.png?raw=1)
    ![Ảnh 31](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/31.png?raw=1)
    ![Ảnh 32](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/32.png?raw=1)
    ![Ảnh 33](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/33.png?raw=1)
    ![Ảnh 34](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/34.png?raw=1)
    ![Ảnh 35](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/35.png?raw=1)
  * Exprot file OpenVPN cho Client
    - Install packet
    ![Ảnh 36](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/36.png?raw=1)
    - Export
    ![Ảnh 37](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/37.png?raw=1)
    ![Ảnh 38](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/38.png?raw=1)
  * Test OpenVPN
    - Trước khi chạy
      ![Ảnh 39](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/39.png?raw=1)
    - Sau khi chạy
      ![Ảnh 40](https://github.com/hiiamtu16/InternReport/blob/aeb8f18622d279dfc3b7cbf7e009c5a95f54cc16/Picture%20/Virtualization/pfSense/40.png?raw=1)

---
  
## VPN Site-to-Site

## pfSense HA

## Kết hợp HA & VPN Site-to-Site

## 
