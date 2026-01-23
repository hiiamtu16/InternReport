# AUTO BACKUP FORTIGATE

## Tạo VM:
  - Tạo VM Ubuntu
  - Tạo thư mục lưu Backup: `/backup/fortinet`
    ```
    sudo mkdir -p /backup/fortinet
    ```
  - Tạo `Server TFTP` nhận backup từ `Fortinet`
    - Cài đặt TFTP Server:
      ```
      sudo apt update
      sudo apt install tftpd-hpa
      ```
    - Chỉnh sửa file cấu hình TFTP:
      ```
      sudo nano /etc/default/tftpd-hpa
      ```
      ```
      TFTP_DIRECTORY="/backup/fortinet"
      TFTP_ADDRESS="0.0.0.0:69"
      TFTP_OPTIONS="--secure --create --permissive"
      ```
    - Cấp quyền:
      ```
      sudo chmod -R 777 /backup/fortinet
      ```
    - Khởi động lại TFTP Server:
      ```
      sudo systemctl restart tftpd-hpa
      ```
---
## Tạo, cấu hình Auto Backup hằng ngày:
  - Tạo file script:
    ```
    nano /usr/local/bin/backup_fgt_tftp.sh
    ```
  - Script Auto backup:
    ```
    #!/bin/bash
    set -euo pipefail
    
    # ===== FORTIGATE CONFIG =====
    FORTI_IP="192.168.254.4"
    FORTI_USER="backup"
    FORTI_PASS="123@123a"
    FW_NAME="fw-600E"
    # ============================
    
    # ===== TFTP SERVER =====
    TFTP_SERVER="172.16.5.11"
    # =======================
    
    # ===== LOCAL DIR (TFTP ROOT DIR) =====
    # Thư mục mà TFTP server đang lưu file (để rotate/xem file)
    TFTP_DIR="/backup/fortinet"
    KEEP=14
    # ================================
    
    # ===== TELEGRAM CONFIG =====
    BOT_TOKEN="8363452772:AAHqUxRL6wmgKjUOR1dQjOQ4r9G4ZbVx7sg"
    CHAT_ID="-5235198580"
    ENABLE_TELEGRAM="yes"   # yes/no
    # ==========================
    
    send_telegram() {
      local MSG="$1"
      [[ "$ENABLE_TELEGRAM" != "yes" ]] && return 0
      curl -s -X POST "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
           -d chat_id="${CHAT_ID}" -d text="${MSG}" >/dev/null || true
    }
    
    DATE="$(date +'%Y-%m-%d_%H-%M-%S')"
    REMOTE_FILE="${FW_NAME}_${DATE}.conf"
    
    echo "[*] Backup FortiGate ${FORTI_IP} -> TFTP ${TFTP_SERVER}"
    echo "[*] Remote filename: ${REMOTE_FILE}"
    
    # Chạy backup từ FortiGate sang TFTP
    set +e
    sshpass -p "$FORTI_PASS" ssh \
      -o StrictHostKeyChecking=no \
      -o UserKnownHostsFile=/dev/null \
      -o ConnectTimeout=15 \
      "${FORTI_USER}@${FORTI_IP}" \
      "execute backup config tftp ${REMOTE_FILE} ${TFTP_SERVER}"
    RET=$?
    set -e
    
    if [[ $RET -ne 0 ]]; then
      echo "[ERR] Backup failed (FortiGate return code: $RET)"
      send_telegram "❌ ${FW_NAME} (${FORTI_IP}): BACKUP FAIL lúc $(date '+%Y-%m-%d %H:%M:%S')"
      exit 1
    fi
    
    echo "[OK] FortiGate sent config to TFTP: ${REMOTE_FILE}"
    
    # Kiểm tra file có xuất hiện trên TFTP server không
    if [[ -d "$TFTP_DIR" ]]; then
      if [[ -f "${TFTP_DIR}/${REMOTE_FILE}" ]]; then
        echo "[OK] File exists on TFTP dir: ${TFTP_DIR}/${REMOTE_FILE}"
      else
        echo "[WARN] File not found in ${TFTP_DIR} (TFTP may save elsewhere)"
      fi
    
      # Rotate giữ KEEP bản (chỉ xóa file đúng pattern fw-600E_*.conf)
      ls -t "${TFTP_DIR}/${FW_NAME}_"*.conf 2>/dev/null | tail -n +$((KEEP+1)) | xargs -r rm -f -- || true
    fi
    
    send_telegram "✅ ${FW_NAME} (${FORTI_IP}): backup OK lúc $(date '+%Y-%m-%d %H:%M:%S'). File: ${REMOTE_FILE}"
    exit 0
    ```
  - Cấp quyền thực thi:
    ```
    chmod +x /usr/local/bin/backup_fgt_tftp.sh
    ```
  - Thực thi lệnh:
    ```
    /usr/local/bin/backup_fgt_tftp.sh
    ```
  - Tạo auto thực thi lệnh:
    - Mở `Crontab`:
      ```
      sudo crontab -e
      ```
    - Thêm lệnh:
      ```
      35 0 * * * /usr/local/bin/backup_fgt_tftp.sh >> /var/log/fortinet254_4.log 2>&1
      ```
    - Kiểm tra:
      ```
      sudo crontab -l
      ```
---
## Chỉnh tạo user backup trên FortiGate:
  ![Ảnh 1](?raw=1)
  ![Ảnh 2](?raw=1)
