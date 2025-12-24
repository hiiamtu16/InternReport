# CHECK MK

## Cài đặt CheckMK trên VM Proxmox
  ### Clone máy Ubuntu trên Proxmox
   - Hướng dẫn tạo máy mới: https://docs.google.com/document/d/1D0eVJVQrEaQ8PoK7FdfM7Of6PUo25itYv34XHWnTZds/edit?tab=t.0#heading=h.ltovxovfl3bv
   - Clone máy từ mẫu tạo sẵn:
    ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/1.png?raw=1)
    ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/2.png?raw=1)
    ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/3.png?raw=1)
    ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/4.png?raw=1)
    ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/5.png?raw=1)
    ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/6.png?raw=1)
  - Log in và SSH vào máy:
    ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/7.png?raw=1)
 
  ### Cài đặt Docker trên máy Ubuntu
   - Cài đặt các gói cần thiết:
    - sudo apt update
    - sudo apt install -y ca-certificates curl gnupg lsb-release
   - Thêm Docker GPG key: sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
| sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   - Thêm Docker repository:echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" \
| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   - Cài Docker Engine:
    -  sudo apt update
    -  sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   - Kiểm tra Docker: docker --version

  ### Cài đặt CheckMK trên máy Ubuntu
   - Link tải CheckMK: https://checkmk.com/download?platform=docker&edition=cce_trial
    ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/8.png?raw=1)
   - Tạo Docker volume cho CheckMK: docker volume create monitoring
   - Chạy Container CheckMK lấy từ Link trên CheckMK: docker container run -dit -p 8080:5000 -p 8000:8000 --tmpfs /opt/omd/sites/cmk/tmp:uid=1000,gid=1000 -v monitoring:/omd/sites --name monitoring -v /etc/localtime:/etc/localtime:ro --restart always checkmk/check-mk-cloud:2.4.0p18
   - Kiểm tra Container: docker ps
   - Xem log lấy pass đăng nhập lần đầu:
    ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/9.png?raw1=)
   - Đổi pass
    ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/10.png?raw1=)
    ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/11.png?raw1=)

---

## Giám sát máy Proxmox
  - Tạo host trên CheckMK
  ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/12.png?raw1=)
  ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/13.png?raw1=)
  - Lấy Agent cho OS cần cài
  ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/14.png?raw1=)
  ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/15.png?raw1=)
  - Cop Agent sang Ubuntu
    - Mở CMD trên PC chạy bằng quyền Admin
    - cd đến folder chứa agent: cd C:\Users\tunv\Downloads
    - Copy file sang Ubuntu VM: scp check-mk-agent_2.4.0p18-dd0c018da230719a_all.deb root@172.16.20.32:/tmp/ (scp "tên file agent" user@"IP Ubuntu":/tmp/)
      ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/16.png?raw1=)
    - Mở PuTTY SSH vào máy Ubuntu, chạy:
      + sudo dpkg -i /tmp/check-mk-agent_2.4.0p18-dd0c018da230719a_all.deb (thay tên file)
      + sudo apt -f install -y
    - Kiểm tra agent: systemctl status check-mk-agent.socket
      ![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/17.png?raw1=)
  - Chạy Monitor trên CheckMK
  ![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/18.png?raw1=)
  ![Ảnh 19](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/19.png?raw1=)

---

## Giám sát PC Windows
  - Tạo host trên CheckMK
  ![Ảnh 20](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/20.png?raw1=)
  - Lấy Agent cho OS cần cài
  ![Ảnh 21](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/21.png?raw1=)
  - Chạy Agent trên PC
  ![Ảnh 22](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/22.png?raw1=)
  - Mở Cổng 6565 Firewall trên PC bằng CMD run as administrator: netsh advfirewall firewall add rule name="Checkmk Agent 6556" dir=in action=allow protocol=TCP localport=6556
  - bật TCP listener (port 6556)
    - Tắt legacy mode: "C:\Program Files (x86)\checkmk\service\check_mk_agent.exe" stop_legacy
    - Bật lại agent chuẩn + listener: "C:\Program Files (x86)\checkmk\service\check_mk_agent.exe" start_legacy
    - Restart service:
      + net stop CheckmkService
      + net start CheckmkService
    - Kiểm tra port 6556: netstat -ano | find "6556" (kết quả: TCP    0.0.0.0:6556     0.0.0.0:0     LISTENING)
  - Kiểm tra Agent đã chạy chưa trên CMD: sc query CheckmkService
  ![Ảnh 23](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/23.png?raw1=)
  - Chạy Monitor trên CheckMK
  ![Ảnh 24](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/24.png?raw1=)
  ![Ảnh 25](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/25.png?raw1=)
  ![Ảnh 26](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/26.png?raw1=)

---

## Giám sát pfSense 
  - Cấu hình SNMP trên pfSense
  ![Ảnh 27](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/27.png?raw1=)
  - Tạo Rule
  ![Ảnh 30](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/30.png?raw1=)
  - Add host pfSense
  ![Ảnh 28](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/28.png?raw1=)
  - Chạy Monitor trên CheckMK
  ![Ảnh 29](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/29.png?raw1=)

## Giám sát máy Ubuntu sau pfSense
  ### Join OpenVPN trên pfSense
   - Cài OpenVPn trên máy Ubuntu chạy Server CheckMK:
    - apt update
    - apt install -y openvpn
    - Kiểm tra: openvpn --version
   - Upload file .ovpn lên máy Ubuntu
    - Mở CMD trên PC chạy bằng quyền Admin
    - cd đến folder chứa file .ovpn: cd C:\Users\tunv\Downloads
    - Copy file sang Ubuntu VM: scp pfSense2-UDP4-1194-user1-config.ovpn root@172.16.20.32:/root/ (scp "tên file ovpn" user@"IP Ubuntu":/root/)
   - Mở OpenVPN trên Proxmox
    - Chạy OpenVPN: openvpn --config /root/pfSense2-UDP4-1194-user1-config.ovpn
    - Log in: user1 / 1
    - End PuTTY, bật lại, kiểm tra:"ip a" check có ip VPN
   - Kiểm tra Rule trên pfSense cho phép dữ liệu qua VPN
    ![Ảnh 31](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/31.png?raw1=)
    ![Ảnh 32](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/32.png?raw1=)
    ![Ảnh 33](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/33.png?raw1=)
   - Kiểm tra Static Route
    ![Ảnh 34](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/34.png?raw1=)
  ### Giám sát máy
  - Tạo host trên CheckMK
  ![Ảnh 35](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/35.png?raw1=)
  - Lấy Agent cho OS cần cài
   ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/14.png?raw1=)
   ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/15.png?raw1=)
  - Cop Agent sang Ubuntu
    - Cài SSH trên máy Ubuntu:
      + sudo apt update
      + sudo apt install -y openssh-server
    - Mở CMD trên PC chạy bằng quyền Admin
    - cd đến folder chứa agent: cd C:\Users\tunv\Downloads
    - Copy file sang Ubuntu VMware: scp check-mk-agent_2.4.0p18-dd0c018da230719a_all.deb linux2@192.168.10.52:/tmp/ (scp "tên file agent" user@"IP Ubuntu":/tmp/)
    - Mở PuTTY SSH vào máy Ubuntu, chạy:
      + sudo dpkg -i /tmp/check-mk-agent_2.4.0p18-dd0c018da230719a_all.deb (thay tên file)
      + sudo apt -f install -y
    - Kiểm tra agent: systemctl status check-mk-agent.socket
      
  - Chạy Monitor trên CheckMK
   ![Ảnh 36](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/36.png?raw1=)
   ![Ảnh 37](https://github.com/hiiamtu16/InternReport/blob/b7d47d9b13fd5d08aebd118cd66c0bf875434a1e/Picture%20/Virtualization/CheckMK/37.png?raw1=)    

## Gửi thông báo về Telegram
  ### Tạo Bot trên Tele
   - Mở Telegram → tìm @BotFather
     - /start
     - /newbot
   - Đặt tên bot: CheckMK BOT
   - Đặt username bot (bắt buộc kết thúc bằng bot): nvtu_checkmk_bot
   - Lưu Bot Token: 8512556584:AAHx3FQBvvGjOUX3SCTBAKC1lyRpjLT8A_M
  ### Lấy Chat ID Tele
   - Tìm bot @userinfobot
    - /start
   - Lấy ID: 7158697854
  ### Tạo Script Tele trên CheckMK
   - SSH vào CheckMK Server từ Docker:
    - docker exec -it monitoring bash
   - Cài nano:
     - apt update
     - apt install nano -y 
   - Vào user cmk: omd su cmk
   - cd đúng thư mục notifications: cd /omd/sites/cmk/local/share/check_mk/notifications
   - Tạo file tele: nano telegram_personal.sh
   - Paste mã:
    #!/bin/bash

BOT_TOKEN="8512556584:AAHx3FQBvvGjOUX3SCTBAKC1lyRpjLT8A_M"
CHAT_ID="7158697854"

#!/bin/bash

BOT_TOKEN="8512556584:AAHx3FQBvvGjOUX3SCTBAKC1lyRpjLT8A_M"
CHAT_ID="7158697854"

STATE="$NOTIFY_SERVICESTATE$NOTIFY_HOSTSTATE"

MESSAGE="🚨 -CheckMK BOT NVTU-

👤 User: $NOTIFY_CONTACTNAME
🖥 Host: -$NOTIFY_HOSTNAME-
🧩 Service: -$NOTIFY_SERVICEDESC-
📊 State: -$STATE-

📝 Output:
$NOTIFY_SERVICEOUTPUT

🕒 Time: $NOTIFY_SHORTDATETIME
"

curl -s -X POST "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
  -d chat_id="${CHAT_ID}" \
  -d parse_mode="Markdown" \
  --data-urlencode text="$MESSAGE"

   - Cấp quyền chạy: chmod +x telegram_personal.sh
   - Test bot:
     - Tìm bot vừa tạo (nvtu_checkmk_bot) nhắn: /start
     - chạy lệnh trên ssh: ./telegram_personal.sh 
  
  ### Cấu hình CheckMK gửi tin nhắn về
   - Tạo Noti:
   ![Ảnh 38](https://github.com/hiiamtu16/InternReport/blob/5c944ef28ba8e69924e9a3ba09255ac794d2ddd5/Picture%20/Virtualization/CheckMK/38.png?raw1=)    
   -  Tạo Rule Noti:
   ![Ảnh 39](https://github.com/hiiamtu16/InternReport/blob/5c944ef28ba8e69924e9a3ba09255ac794d2ddd5/Picture%20/Virtualization/CheckMK/39.png?raw1=)
   ![Ảnh 40](https://github.com/hiiamtu16/InternReport/blob/5c944ef28ba8e69924e9a3ba09255ac794d2ddd5/Picture%20/Virtualization/CheckMK/40.png?raw1=)    
   ![Ảnh 41](https://github.com/hiiamtu16/InternReport/blob/5c944ef28ba8e69924e9a3ba09255ac794d2ddd5/Picture%20/Virtualization/CheckMK/41.png?raw1=)    
   ![Ảnh 42](https://github.com/hiiamtu16/InternReport/blob/5c944ef28ba8e69924e9a3ba09255ac794d2ddd5/Picture%20/Virtualization/CheckMK/42.png?raw1=)    
   -  Test Noti
     - Rescan:
     ![Ảnh 43](https://github.com/hiiamtu16/InternReport/blob/5c944ef28ba8e69924e9a3ba09255ac794d2ddd5/Picture%20/Virtualization/CheckMK/43.png?raw1=)
     - Active device:
     ![Ảnh 44](https://github.com/hiiamtu16/InternReport/blob/5c944ef28ba8e69924e9a3ba09255ac794d2ddd5/Picture%20/Virtualization/CheckMK/44.png?raw1=)    
     - Test Noti:
     ![Ảnh 45](https://github.com/hiiamtu16/InternReport/blob/eaae9fe92c97faf10761d8a27a3a264707a3b778/Picture%20/Virtualization/CheckMK/45.png?raw1=)    
  - Kết quả BOT: 
  ![Ảnh 46](https://github.com/hiiamtu16/InternReport/blob/ab565fcae7a526d49f9076a2c82dcad5a21973ff/Picture%20/Virtualization/CheckMK/46.jpg?raw1=)    

## Thay đổi mức WARN / CRIT
  - Add Rule:
    ![Ảnh 46](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/46.png?raw1=)
    - Tìm Filesystem
    ![Ảnh 47](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/47.png?raw1=)
    ![Ảnh 48](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/48.png?raw1=)
    - Add
    ![Ảnh 49](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/49.png?raw1=)
    ![Ảnh 50](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/50.png?raw1=)
  - Kiểm tra
    - WARN ở mức >60%
    ![Ảnh 59](https://github.com/hiiamtu16/InternReport/blob/741dea63cd7bb5e418276109431c7b31b47c8ba1/Picture%20/Virtualization/CheckMK/59.png?raw1=)
    - CRIT ở mức >80%
    ![Ảnh 60](https://github.com/hiiamtu16/InternReport/blob/741dea63cd7bb5e418276109431c7b31b47c8ba1/Picture%20/Virtualization/CheckMK/60.png?raw1=)

## Giám sát Web LogicLab
  - Tạo Host
  ![Ảnh 51](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/51.png?raw1=)
  - Tạo Rule:
  ![Ảnh 52](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/52.png?raw1=)
  ![Ảnh 53](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/53.png?raw1=)
  - Rule:
  ![Ảnh 54](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/54.png?raw1=)
  ![Ảnh 55](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/55.png?raw1=)
  - Kiểm tra:
    - Scan
    ![Ảnh 56](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/56.png?raw1=)
    - Check 
    ![Ảnh 57](https://github.com/hiiamtu16/InternReport/blob/2bce95dde11147acc354e71a1bdd79db80f074ba/Picture%20/Virtualization/CheckMK/57.png?raw1=)

##  Connect CheckMK - Grafana
  - Cài Grafana trên máy CheckMK Server
    - update hệ thống:
      - sudo apt update
      - sudo apt upgrade -y
    - Cài các gói cần thiết
      - sudo apt install -y apt-transport-https software-properties-common wget
    - Thêm Grafana GPG key
      - wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
    - Thêm Grafana repository
      - echo "deb https://packages.grafana.com/oss/deb stable main" \
| sudo tee /etc/apt/sources.list.d/grafana.list
    - Cài Grafana
      - sudo apt update
      - sudo apt install grafana -y
    - Khởi động Grafana
      - sudo systemctl enable grafana-server
      - sudo systemctl start grafana-server
    - Kiểm tra Grafana đã chạy chưa
      - systemctl status grafana-server
      - Kết quả: Active: active (running); cổng 3000
    - Check Port Grafana: netstat -lntp | grep 3000
  - Mở Grafana trên Web GUI:
    - Truy cập Grafana trên web: 172.16.20.32:3000 (IP máy cài grafana:port 3000)
    - TK mặc định đăng nhập lần đầu: admin / admin
    - Đổi mk mặc định
  - Cài Plugin
    - Tìm plug in checkmk
    ![Ảnh 61](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/61.png?raw1=)
    ![Ảnh 62](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/62.png?raw1=)
  - Cấu hình user cho Grafana trên CheckMK
    ![Ảnh 63](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/63.png?raw1=)
    ![Ảnh 64](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/64.png?raw1=)
  - Add Datasource:
    ![Ảnh 65](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/65.png?raw1=)
    ![Ảnh 66](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/66.png?raw1=)
    ![Ảnh 67](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/67.png?raw1=)
  - Tạo Dashboard
    ![Ảnh 68](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/68.png?raw1=)
    ![Ảnh 69](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/69.png?raw1=)
    ![Ảnh 70](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/70.png?raw1=)
  - Vẽ biểu đồ
    ![Ảnh 71](https://github.com/hiiamtu16/InternReport/blob/417677eea702b0be6d52d1bd9a2d0ab90c0abe6c/Picture%20/Virtualization/CheckMK/71.png?raw1=)
    
    
           
   
