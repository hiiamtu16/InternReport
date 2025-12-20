# VPN SITE - TO - SITE OMADA - MIKROTIK

## Cấu hình IPsec trên Omada
  * Cấu hình IPsec
  ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/1.png?raw=1)
  * Cấu hình Phase1 và Phase2
  ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/2.png?raw=1)

---

## Cấu hình IPsec trên MikroTik
  ### Cấu hình IPsec
  * Tạo Profile IPsec (giống Phase1 bên Omada)
  ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/3.png?raw=1)
  ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/4.png?raw=1)
  * Tạo peer IKEv2 trỏ về Omada
  ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/5.png?raw=1)
  ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/6.png?raw=1)
  * Tạo identity dùng pre shared key
  ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/7.png?raw=1)
  ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/8.png?raw=1)
  * Tạo proposal Phase 2 (ở đây dùng cái có sẵn đã tạo cùng 1 cấu hình, dùng lại trên nhiều con)
  ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/9.png?raw=1)
  ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/10.png?raw=1)
  * Tạo policy để match traffic giữa 2 mạng
  ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/11.png?raw=1)
  ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/12.png?raw=1)
  ### Cấu hình NAT
  * Cấu hình NAT bypass để không bị masquerade đi vào tunnel
  ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/13.png?raw=1)
  ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/14.png?raw=1)
  ### Kiểm tra Rule
  * Mở firewall cho IPsec đi qua input
  ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/15.png?raw=1)

---

## Kiểm tra
  * Trên Omada:
  ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/486d7714c8d3ebaa516db32375bb55c99c341e08/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/Site%20to%20Site%20Omada%20-%20MikroTik/16.png?raw=1)
  * Ping thông từ mạng Private MikroTik --> Omada (đã test máy anh Cường)
