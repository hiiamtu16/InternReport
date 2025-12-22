# CHECK MK

## Cài đặt CheckMK trên VM Proxmox
  ### Clone máy Ubuntu trên Proxmox
   * Hướng dẫn tạo máy mới: https://docs.google.com/document/d/1D0eVJVQrEaQ8PoK7FdfM7Of6PUo25itYv34XHWnTZds/edit?tab=t.0#heading=h.ltovxovfl3bv
   * Clone máy từ mẫu tạo sẵn:

  ### Cài đặt Docker trên máy Ubuntu
   * Cài đặt các gói cần thiết:
    - sudo apt update
    - sudo apt install -y ca-certificates curl gnupg lsb-release
   * Thêm Docker GPG key: sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
| sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   * Thêm Docker repository:echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" \
| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   * Cài Docker Engine:
    - sudo apt update
    - sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   * Kiểm tra Docker: docker --version

  ### Cài đặt CheckMK trên máy Ubuntu
   * Link tải CheckMK: https://checkmk.com/download?platform=docker&edition=cce_trial
   * Tạo Docker volume cho CheckMK: docker volume create monitoring
   * Chạy Container CheckMK lấy từ Link trên CheckMK: docker container run -dit -p 8080:5000 -p 8000:8000 --tmpfs /opt/omd/sites/cmk/tmp:uid=1000,gid=1000 -v monitoring:/omd/sites --name monitoring -v /etc/localtime:/etc/localtime:ro --restart always checkmk/check-mk-cloud:2.4.0p18
   * Kiểm tra Container: docker ps
   * Xem log lấy pass đăng nhập lần đầu:
    ![Ảnh ](?raw1=)
   * Đổi pass 
---

## Giám sát máy Proxmox
  * Tạo host trên CheckMK

  * Lấy Agent cho OS cần cài

  * Cop Agent sang Ubuntu
    - Mở CMD trên PC chạy bằng quyền Admin
    - cd đến folder chứa agent: cd C:\Users\tunv\Downloads
    - Copy file sang Ubuntu VM: scp check-mk-agent_2.4.0p18-dd0c018da230719a_all.deb root@172.16.20.32:/tmp/ (scp "tên file agent" user@"IP Ubuntu":/tmp/)
      
    - Mở PuTTY SSH vào máy Ubuntu, chạy:
      + sudo dpkg -i /tmp/check-mk-agent_2.4.0p18-dd0c018da230719a_all.deb (thay tên file)
      + sudo apt -f install -y
    - Kiểm tra agent: systemctl status check-mk-agent.socket
      
  * Chạy Monitor trên CheckMK
    
---

## Giám sát PC Windows
  * Tạo host trên CheckMK

  * Lấy Agent cho OS cần cài

  * Chạy Agent trên PC

  * Mở Cổng 6565 Firewall trên PC bằng CMD run as administrator: netsh advfirewall firewall add rule name="Checkmk Agent 6556" dir=in action=allow protocol=TCP localport=6556
  * bật TCP listener (port 6556)
    - Tắt legacy mode: "C:\Program Files (x86)\checkmk\service\check_mk_agent.exe" stop_legacy
    - Bật lại agent chuẩn + listener: "C:\Program Files (x86)\checkmk\service\check_mk_agent.exe" start_legacy
    - Restart service:
      + net stop CheckmkService
      + net start CheckmkService
    - Kiểm tra port 6556: netstat -ano | find "6556" (kết quả: TCP    0.0.0.0:6556     0.0.0.0:0     LISTENING)
  * Kiểm tra Agent đã chạy chưa trên CMD: sc query CheckmkService
  * Chạy Monitor trên CheckMK
    

---

## Giám sát pfSense 
  * Cấu hình SNMP trên pfSense

  * Cấu hình SNMP trên pfSense

  * Tạo Rule

  * Add host pfSense

  * Chạy Monitor trên CheckMK

## Giám sát máy Ubuntu sau pfSense
  ### Join OpenVPN trên pfSense
   * Cài OpenVPn trên máy Ubuntu chạy CheckMK:
    - apt update
    - apt install -y openvpn
    - Kiểm tra: openvpn --version
   * Upload file .ovpn lên máy Ubuntu
    - Mở CMD trên PC chạy bằng quyền Admin
    - cd đến folder chứa file .ovpn: cd C:\Users\tunv\Downloads
    - Copy file sang Ubuntu VM: scp pfSense2-UDP4-1194-user1-config.ovpn root@172.16.20.32:/root/ (scp "tên file ovpn" user@"IP Ubuntu":/root/)
   * Mở OpenVPN trên Proxmox
    - Chạy OpenVPN: openvpn --config /root/pfSense2-UDP4-1194-user1-config.ovpn
    - Log in: user1 / 1
    - End PuTTY, bật lại, kiểm tra:"ip a" check có ip VPN
   * Kiểm tra Rule trên pfSense cho phép dữ liệu qua VPN
   * Kiểm tra Static Route
  ### Giám sát máy
  * Tạo host trên CheckMK

  * Lấy Agent cho OS cần cài

  * Cop Agent sang Ubuntu
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
      
  * Chạy Monitor trên CheckMK
    
    
