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
  ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/97fda7a4cf031c2dc8bbbf4968da8d46dcce5722/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/Gi%C3%A1m%20s%C3%A1t%20voip.apps.vm%20v%C3%A0%20OpenVox%20Gateway/1.png?raw=1)
- Trên Server CheckMK:
  ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/97fda7a4cf031c2dc8bbbf4968da8d46dcce5722/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/Gi%C3%A1m%20s%C3%A1t%20voip.apps.vm%20v%C3%A0%20OpenVox%20Gateway/2.png?raw=1)
  ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/97fda7a4cf031c2dc8bbbf4968da8d46dcce5722/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/Gi%C3%A1m%20s%C3%A1t%20voip.apps.vm%20v%C3%A0%20OpenVox%20Gateway/3.png?raw=1)
  ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/97fda7a4cf031c2dc8bbbf4968da8d46dcce5722/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/Gi%C3%A1m%20s%C3%A1t%20voip.apps.vm%20v%C3%A0%20OpenVox%20Gateway/4.png?raw=1)
## Giám sát server OpenVox Gateway
- IP: 172.16.95.20
- Cấu hình SNMP
  ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/97fda7a4cf031c2dc8bbbf4968da8d46dcce5722/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/Gi%C3%A1m%20s%C3%A1t%20voip.apps.vm%20v%C3%A0%20OpenVox%20Gateway/5.png?raw=1)
- Trên Server CheckMK
  ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/97fda7a4cf031c2dc8bbbf4968da8d46dcce5722/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/Gi%C3%A1m%20s%C3%A1t%20voip.apps.vm%20v%C3%A0%20OpenVox%20Gateway/6.png?raw=1)
  ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/97fda7a4cf031c2dc8bbbf4968da8d46dcce5722/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/Gi%C3%A1m%20s%C3%A1t%20voip.apps.vm%20v%C3%A0%20OpenVox%20Gateway/7.png?raw=1)
