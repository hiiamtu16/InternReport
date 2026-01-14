# Tạo và cấu hình Ubuntu

## Download và Upload
  - Link down Ubuntu: https://releases.ubuntu.com/plucky/
  - Chọn bản Server install image
  ![Ảnh 1](?raw=1)
  - Upload Ubuntu lên Storage Proxmox
  ![Ảnh 2](?raw=1)
  - Khởi tạo VM Ubuntu:
    - Đặt tên VM:
    ![Ảnh 3](?raw=1)
    - Chọn ISO
    ![Ảnh 4](?raw=1)
    - Tạo Disk nhỏ (25GB)
    ![Ảnh 5](?raw=1)
    - CPU 1 socket 4 cores
    ![Ảnh 6](?raw=1)
    - RAM 4GB
    ![Ảnh 7](?raw=1)
    - Network
    ![Ảnh 8](?raw=1)

## Setup Ubuntu:
  - Cấu hình theo ảnh
  ![Ảnh 9](?raw=1)
  ![Ảnh 10](?raw=1)
  ![Ảnh 11](?raw=1)
  - Cấu hình IP tĩnh:
    - Xem card mạng và ip đang chạy: ip a
    - Mở file netplan: sudo nano /etc/netplan/*.yaml
    - đặt ip tĩnh:
     ```
     network:
      version: 2
      renderer: networkd
      ethernets:
        ens18:
          dhcp4: false
          addresses:
            - 10.1.35.3/24
          routes:
            - to: default
              via: 10.1.35.1
          nameservers:
            addresses:
              - 8.8.8.8
              - 1.1.1.1
     ```
    - thoát và save
    - apply:
    ```
    sudo netplan apply
    ip a
    ip route
    ```
  - Cho phép SSH từ tài khoản root:
    - Tạo pass cho root: sudo passwd root
    - Cài editor:
      - sudo apt update
      - sudo apt install -y nano
    - Sửa cấu hình SSH: sudo nano /etc/ssh/sshd_config
      - Tìm PermitRoot:
      - bật:
        - PermitRootLogin yes
        - PasswordAuthentication yes
      - Test & Restart:
        - sudo sshd -t
        - sudo systemctl restart ssh

  ## Cài CHECK MK
  - Cài check mk theo yêu cầu:
    ```
    sudo apt update
    sudo apt install -y ca-certificates curl gnupg lsb-release
    sudo mkdir -p /etc/apt/keyrings
    
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
    | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
    https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" \
    | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    
    sudo apt update
    sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
    ```
  - Kiểm tra:
  ```
  docker --version
  docker compose version
  ```
  - Tạo thư mục lưu theo yêu cầu:
  ```
  sudo mkdir -p /opt/checkmk/data
  cd /opt/checkmk
  ```
  - Tạo file docker-compose.yml:
  ```
  nano docker-compose.yml
  ```
  - Paste vào:
  ```
  version: '3.8'
    services:
      checkmk:
        image: checkmk/check-mk-raw:2.4.0p3
        container_name: checkmk
        restart: always
        ports:
          - "8080:5000"
          - "8000:8000"
        tmpfs:
          - /opt/omd/sites/monitoring/tmp:uid=1000,gid=1000
        volumes:
          - /opt/checkmk/data:/omd/sites
        environment:
          CMK_SITE_ID: monitoring
          CMK_PASSWORD: admin123
          CMK_LIVESTATUS_TCP: "on"
  ```
  - Chạy CheckMK:
  ```
  docker compose up -d
  ```

## Backup Wasabi và Restore từ Proxmox khác
  - Tạo DataStore trên PBS:
    - Tạo S3 endpoint chung để backup & restore
    ![Ảnh 12](?raw=1)
    - Tạo Local Cache (mở SSH PBS):
     ```
     mkdir -p /var/lib/proxmox-backup/wasabi/clone-checkmk
    chown -R backup:backup /var/lib/proxmox-backup
    chmod 700 /var/lib/proxmox-backup
     ```
    - Copy đường dẫn cho Local Cache: /var/lib/proxmox-backup/wasabi/clone-checkmk
    ![Ảnh 13](?raw=1)
  - Link PVE vs PBS:
    ![Ảnh 14](?raw=1)
    ![Ảnh 15](?raw=1)
  - Back up:   ![Ảnh 16](?raw=1)
  
## Restore trên PVE khác
  - Xoá .in-use trên Wasabi bucket
  ![Ảnh 17](?raw=1)
  - Add S3 endpoint và tạo DataStore trên PVE muốn dùng để restore (như trên)
  - Restore dữ liệu về (Doc: https://github.com/hiiamtu16/InternReport/blob/40ea60981f1a60c4da1851c30288b50424e31952/Virtualization/Restore%20with%20RClone%20Wasabi.md)
  - Đổi IP tĩnh
  - Đổi hostname:
    - Đổi hostname trong 2 file:
      - sudo nano /etc/hostname
      - sudo nano /etc/hosts
    - Reboot: sudo reboot    

## Tạo Remote Backup trên PBS
  - Tạo thư mục lưu: sudo mkdir -p /mnt/datastore/clone-vm/
  - Cấp quyền: sudo chown -R www-data:www-data /mnt/datastore/
  - Backing Path cop vào PBS: /mnt/datastore/clone-vm/
  - Lấy IP Public của PBS: curl ifconfig.me
  - Tạo remote:   ![Ảnh 18](?raw=1)
  - Tạo Pull Sync job trong DataStore trên PBS và run now để sync

## Tạo Bot gửi thông báo Group
  - Tạo thư mục: sudo mkdir -p /omd/sites/monitoring/local/share/check_mk/notifications
  - Tạo file Script: sudo nano /omd/sites/monitoring/local/share/check_mk/notifications/telegram_notify.sh
  - Nội dung Script:
    ```
    #!/bin/bash

    # Telegram notification for CheckMK (Vietnamese - Docker version, numeric-state compatible)
    # Author: ChatGPT x CuongNV (Final verified 2025)
    
    # Thông tin Bot và ChatID
    BOT_TOKEN="8355983239:AAE9KZneAscdCjWC0f5baoJTI0fN_ZJMtLo"
    CHAT_ID="-1003588408038"
    LOGFILE="/omd/sites/monitoring/var/log/telegram_notify.log"
    TMP_FLAG="/tmp/checkmk_${NOTIFY_HOSTNAME}.flag"
    
    # Cài đặt múi giờ cho Việt Nam
    export TZ="Asia/Ho_Chi_Minh"
    CURRENT_TIME=$(date +"%H:%M - %d/%m/%Y")
    
    # ===============================
    # 1. Xác định trạng thái (CheckMK đôi khi chỉ truyền số)
    # ===============================
    STATE="${NOTIFY_STATE:-${NOTIFY_SHORTSTATE}}"
    TYPE="${NOTIFY_NOTIFICATIONTYPE:-UNKNOWN}"
    
    case "$STATE" in
      0|OK|UP)
        STATE="OK"
        ICON="✅"
        TITLE="ĐÃ KHÔI PHỤC"
        ;;
      1|WARN)
        STATE="WARN"
        ICON="🟡"
        TITLE="CẢNH BÁO"
        ;;
      2|CRIT|DOWN)
        STATE="CRIT"
        ICON="🔴"
        TITLE="CẢNH BÁO NGHIÊM TRỌNG"
        ;;
      3|UNKNOWN)
        STATE="UNKNOWN"
        ICON="⚫"
        TITLE="KHÔNG XÁC ĐỊNH"
        ;;
      *)
        ICON="🔔"
        TITLE="THÔNG BÁO"
        ;;
    esac
    
    # Nếu là thông báo khôi phục (RECOVERY) thì ép lại tiêu đề/icon cho rõ
    if [[ "$TYPE" == "RECOVERY" ]]; then
        ICON="✅"
        TITLE="ĐÃ KHÔI PHỤC"
    fi
    
    # ===============================
    # 2. Chống gửi trùng (cho HOST)
    # ===============================
    if [ "$NOTIFY_WHAT" = "HOST" ]; then
        if [ -f "$TMP_FLAG" ] && [ "$STATE" != "OK" ]; then
            echo "$(date '+%F %T') - Skip duplicate host alert for ${NOTIFY_HOSTNAME}" >> "$LOGFILE"
            exit 0
        fi
        if [ "$STATE" != "OK" ]; then
            echo "recent" > "$TMP_FLAG"
            (sleep 15 && rm -f "$TMP_FLAG") &
        fi
    fi
    
    # ===============================
    # 3. Xây nội dung tin nhắn
    # ===============================
    if [ "$NOTIFY_WHAT" = "SERVICE" ]; then
        MESSAGE="📢 *Thông báo từ CheckMK SG*\n\n
    *Trạng thái*: $TITLE\n
    *Máy chủ*: ${NOTIFY_HOSTNAME}\n
    *Dịch vụ*: ${NOTIFY_SERVICEDESC}\n
    *Nội dung*: ${NOTIFY_SERVICEOUTPUT}\n
    *Thời gian*: ${CURRENT_TIME}"
    else
        MESSAGE="📢 *Thông báo từ CheckMK SG*\n\n
    *Trạng thái*: $TITLE\n
    *Máy chủ*: ${NOTIFY_HOSTNAME}\n
    *Nội dung*: ${NOTIFY_HOSTOUTPUT}\n
    *Thời gian*: ${CURRENT_TIME}"
    fi
    
    # ===============================
    # 4. Kiểm tra nội dung tin nhắn trước khi gửi
    # ===============================
    if [ -z "$MESSAGE" ]; then
        echo "$(date '+%F %T') - Error: Message is empty for ${NOTIFY_HOSTNAME}" >> "$LOGFILE"
        exit 1
    fi
    
    # ===============================
    # 5. Gửi Telegram
    # ===============================
    # Kiểm tra thông điệp có đúng không trước khi gửi
    echo "Sending message to Telegram with content: $MESSAGE" >> "$LOGFILE"
    
    curl -s -X POST "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
         --data-urlencode "chat_id=${CHAT_ID}" \
         --data-urlencode "text=${MESSAGE}" \
         --data-urlencode "parse_mode=Markdown" >> "$LOGFILE" 2>&1
    
    # Ghi vào log khi đã gửi thông báo
    echo "$(date '+%F %T') - Sent ${STATE}/${TYPE} alert for ${NOTIFY_WHAT}/${NOTIFY_HOSTNAME}" >> "$LOGFILE"
    ```
    - Cập nhật quyền thực thi: sudo chmod +x /omd/sites/monitoring/local/share/check_mk/notifications/telegram_notify.sh
    
