# Cấu hình VPN Client - to - Site Router TP-Link OMADA ER8411

## Cấu hình router
### Cấu hình WAN và quay PPPoE trên Router Omada
* Truy cập Web GUI chọn đường WAN trên Router
  ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/1.png?raw=1)
* Cấu hình quay PPPoE trên cổng WAN
  ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/2.png?raw=1)
### Cấu hình dải LAN, VLAN và DHCP Server
* Tạo dải LAN, gắn VLAN và cấu hình DHCP Server
  ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/3.png?raw=1)
* Gán cổng VLAN
  ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/4.png?raw=1)
## Cấu hình OpenVPN
### Tạo Server OpenVPN trên Omada
  * Cấu hình Server OpenVPN và xuất file cấu hình cho người dùng
    ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/5.png?raw=1)
  * Tạo user cho OpenVPN
    ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/7.png?raw=1)
### Join VPN và kết quả
  * Sử dụng phần mềm OpenVPN
    ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/6.png?raw=1)
  * Đăng nhập OpenVPN bằng username và password đã tạo trên Omada
    ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/8.png?raw=1)
  * Kết quả
    ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/d314c0db3f955d3cfea281d5b669ea878f3bcde2/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/9.png?raw=1)

---

# Cấu hình VPN Client - to - Site Firewall Fortinet FortiGate-300E

## Cấu hình Firewall
  * Tạo tài khoản cho user
    ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/1.png?raw=1)
    ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/2.png?raw=1)
  * Tạo Nhóm cho người dùng
    ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/3.png?raw=1)

## Cấu hình FortiClient
  * Tạo IPsec Tunnel VPN
    ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/4.png?raw=1)
    ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/5.png?raw=1)
    ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/6.png?raw=1)
    ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/7.png?raw=1)
    ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/8.png?raw=1)
  * Route và Policy
    - Tạo 2 Policy từ LAN đến VPN và ngược lại
      ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/9.png?raw=1)

## Truy cập VPN bằng FortiClient
  * Thêm cấu hình VPN
    ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/10.png?raw=1)
    ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/11.png?raw=1)
    ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/12.png?raw=1)
    ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/13.png?raw=1)
  * Đăng nhập vào bằng Username và Password đã tạo trên FortiGate
    ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/14.png?raw=1)

## Kiểm tra kết quả
  * Tạo được kết nối VPN giữa PC và FortiGate, lấy được IP Private trên FortiGate
  ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/b93873c6b59114ef4698c873f72d80902b259fef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Client-to-Site%20FortiGate%20300E/15.png?raw=1)






