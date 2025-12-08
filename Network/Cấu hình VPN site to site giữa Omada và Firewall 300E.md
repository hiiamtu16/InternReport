# 1. Cấu hình VPN Site-to-Site trên Omada
  * Cấu hình IPsec
    ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/1.png?raw=1)
  * Cấu hình và lưu thông số trên Phase1 và Phase2
    ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/2.png?raw=1)
    
---

# 2. Cấu hình VPN Site-to-Site trên FortiGate 300E
  ## Tạo Tunnel
  ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/3.png?raw=1)
  ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/4.png?raw=1)
  * Cấu hình thông số trên Phase1 và Phase2 giống với trên Omada
  ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/5.png?raw=1)
  ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/6.png?raw=1)
 
  ## Đặt Firewall Policy
  * Policy từ Port LAN đến VPN
  ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/7.png?raw=1)
  * Policy từ VPN đến Port LAN
  ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/8.png?raw=1)

  ## Đặt Static Route đến IP Private trên Omada qua VPN
  ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/9.png?raw=1)

---

# 3. Kiểm tra kết quả
  ## Kiểm tra trên Router và Firewall
  * Trên Omada
    ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/10.png?raw=1)
  * Trên FortiGate 300E
    ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/11.png?raw=1)
  ## Kiểm tra ping
  *Ping thông từ thiết bị lấy IP trên FortiGate đến 2 ip của Omada
    ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/13b9f5387cacaf7ea8cc99cb079c410049a23f7e/Picture%20/Network%20/C%E1%BA%A5u%20h%C3%ACnh%20VPN/VPN%20Site-to-Site%20Omada%20-%20FortiGate/12.png?raw=1)
