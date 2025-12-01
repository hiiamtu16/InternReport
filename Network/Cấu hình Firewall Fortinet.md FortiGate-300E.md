1. Kết nối
  - Kết nối máy tính với cổng MGMT bằng dây chuẩn RJ45
  - show ipconfig trên CMD, lấy địa chỉ default gateway để truy cập trên http:// (Default Gateway)
      https://github.com/hiiamtu16/InternReport/blob/3c10797db448de5b8658e702e81872fe4fd88dbf/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/CheckIP.png
  - Đăng nhập web bằng username: admin / pass: hpa#2025
      https://github.com/hiiamtu16/InternReport/blob/043bdcd189c99fe0b11615a7d470e4fc421be96e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/login_web.png
2. Tạo Interface
  * Tạo WAN Interface:
    - Chọn cổng kết nối từ modem ISP nối với Firewall
      https://github.com/hiiamtu16/InternReport/blob/265780e8ccbfa4c8fe45a07d777526fa013accef/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/choosePort.png
    - Chọn Edit và thiết lập các thông số trên cổng
      + Đặt Alias (Tên để dễ quản lý), chọn Role WAN, Address chọn PPPOE
      + Nhập Username và Password do nhà mạng cung cấp, Nếu là FPT gọi 1900 6600 nhánh 2 yêu cầu Reset MAC (cung cấp mã số thuế và tên tài khoản)
      + Chọn dịch vụ cho phép trên Firewall
        https://github.com/hiiamtu16/InternReport/blob/04c750708a7876a86d4aea5ff2ac1f3d7b1ac2a5/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Edit%20Interface%201.png
        https://github.com/hiiamtu16/InternReport/blob/04c750708a7876a86d4aea5ff2ac1f3d7b1ac2a5/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Edit%20Interface%202.png
  * Tạo Loopback Interface:
    https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/create%20loopback%20interface.png
    - Điền các thông tin theo ảnh hướng dẫn:
    https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/interface%20loopback.png
3. Tạo SD-WAN
  * Tạo SD-WAN Zones
    - https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Create%20SD-WAN%20Zones.png
    - https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/New%20Zones.png
  * Tạo SD-WAN Member
    - https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Create%20SD-WAN%20Member.png
    - https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/New%20Member.png
  * Tạo Performance SLAs
    -  https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Create%20SLAs.png
    -  https://github.com/hiiamtu16/InternReport/blob/20af1722440200fc8cb1c281ab0530f9d87807ce/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/New%20SLA.png
  * Tạo SD-WAN Rule
    -  https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/create%20rule.png
    -  https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/priority%20rule.png
    -  https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/priority%20rule%202.png
4. Tạo Policy
  - Mỗi Policy chỉ áp dụng cho 1 IP Loopback. Nếu muốn tạo 1 Pool Loopback phải tạo từng IP Loopback
  - https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/Firewall%20Policy.png
  - https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/edit%20policy.png
  - https://github.com/hiiamtu16/InternReport/blob/a94800db588edccf287be1bdccd4bfc1429ced56/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20Firewall%20Fortinet%20FortiGate-300E/edit%20policy%202.png



