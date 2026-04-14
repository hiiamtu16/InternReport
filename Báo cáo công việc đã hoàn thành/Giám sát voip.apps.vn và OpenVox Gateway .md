## Giám sát server HAPBX:
- IP: 172.16.95.19
- Agent đã cài:
  - Lệnh cài Agent:
   ```
   sudo dpkg -i /tmp/check-mk-agent_2.4.0p3-1_all.deb
   sudo apt -f install -y
   ```
  - Kiểm tra Agent:
    ```
    systemctl status check-mk-agent.socket
    ```
- Trạng thái Agent
  ![Ảnh 1](?raw=1)
- Trên Server CheckMK:
  ![Ảnh 2](?raw=1)
  ![Ảnh 3](?raw=1)
  ![Ảnh 4](?raw=1)
## Giám sát server OpenVox Gateway
- IP: 172.16.95.20
- Cấu hình SNMP
  ![Ảnh 5](?raw=1)
- Trên Server CheckMK
  ![Ảnh 6](?raw=1)
  ![Ảnh 7](?raw=1)
