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
    nano /usr/local/bin/backup_fgt_pw.sh
    ```
  - Script Auto backup:
    ```
    #!/bin/bash
    set -euo pipefail
    
    # ===== FORTIGATE CONFIG =====       
    FORTI_IP="192.168.254.4"
    FORTI_USER="backup"  # Dùng tài khoản có quyền sao lưu (backup)
    FORTI_PASS="123@123a"
    USE_GLOBAL="no"
    # ============================
    
    # ===== BACKUP SERVER (THIS MACHINE) =====
    BACKUP_DIR="/backup/fortinet"
    TFTP_SERVER="172.16.5.11"        # IP máy cài TFTP server
    TFTP_PATH="/backup/fortinet"     # Đường dẫn lưu file
    KEEP=14
    # ============================
    
    # ===== TELEGRAM CONFIG =====
    BOT_TOKEN="8363452772:AAHqUxRL6wmgKjUOR1dQjOQ4r9G4ZbVx7sg"      # Thay token bot khác
    CHAT_ID="5557990974"                                            # Thay ID chat khác
    # ==========================
    
    mkdir -p "$BACKUP_DIR"
    DATE="$(date +'%Y-%m-%d_%H-%M-%S')"
    BASENAME="fortigate_${FORTI_IP}_${DATE}.conf"
    OUT_FILE="${BACKUP_DIR}/${BASENAME}"
    OUT_GZ="${OUT_FILE}.gz"  # File nén sẽ có đuôi .conf.gz
    OUT_SHA="${OUT_FILE}.sha256"
    
    send_telegram() {
      local MSG="$1"
      curl -s -X POST "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
           -d chat_id="${CHAT_ID}" -d text="${MSG}" >/dev/null || true
    }
    
    echo "[*] Request FortiGate to backup config to TFTP server ${TFTP_SERVER}..."
    
    # FortiGate sẽ PUSH file về TFTP server (máy Auto)
    # Lưu ý: FortiGate sẽ hỏi password của user SCP trên server (backupuser)
    # => Cách chuẩn nhất là dùng SSH key (khuyến nghị). Nếu chưa dùng key thì sẽ fail.
    REMOTE_CMD=""
    
    if [[ "$USE_GLOBAL" == "yes" ]]; then
      REMOTE_CMD=$'config global\n'
    fi
    
    # Lệnh sao lưu vào bộ nhớ flash của FortiGate (đảm bảo tạo ra file cấu hình hợp lệ)
    REMOTE_CMD+=$"execute backup config tftp ${BASENAME} ${TFTP_SERVER}\n"
    
    # chạy lệnh trên FortiGate
    /usr/bin/sshpass -p "$FORTI_PASS" ssh \
      -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o ConnectTimeout=15 \
      "${FORTI_USER}@${FORTI_IP}" \
      "${REMOTE_CMD}" || true
    
    # Đợi file xuất hiện trên TFTP server (máy Auto)
    for i in {1..20}; do
      [[ -s "$OUT_FILE" ]] && break
      sleep 1
    done
    
    if [[ ! -s "$OUT_FILE" ]]; then
      send_telegram "⚠️ Fortinet ${FORTI_IP}: backup FAIL lúc $(date '+%Y-%m-%d %H:%M:%S'). Không nhận được file sao lưu từ TFTP."
      exit 1
    fi
    
    # Nén file sao lưu thành định dạng .gz
    gzip -f "$OUT_FILE"  # Nén file sao lưu
    
    # Tạo checksum cho file đã nén
    sha256sum "$OUT_GZ" > "$OUT_SHA"
    
    # rotate giữ KEEP bản
    ls -t "${BACKUP_DIR}/fortigate_${FORTI_IP}_"*.conf.gz 2>/dev/null | tail -n +$((KEEP+1)) | xargs -r rm -f -- || true
    ls -t "${BACKUP_DIR}/fortigate_${FORTI_IP}_"*.conf.gz.sha256 2>/dev/null | tail -n +$((KEEP+1)) | xargs -r rm -f -- || true
    
    echo "[OK] Saved $(basename "$OUT_GZ")"
    send_telegram "✅ Fortinet ${FORTI_IP}: backup OK lúc $(date '+%Y-%m-%d %H:%M:%S'). File: $(basename "$OUT_GZ")."
    ```
  - Cấp quyền thực thi:
    ```
    chmod +x nano /usr/local/bin/backup_fgt_pw.sh
    ```
  - Thực thi lệnh:
    ```
    /usr/local/bin/backup_fgt_pw.sh
    ```
  - Tạo auto thực thi lệnh:
    - Mở `Crontab`:
      ```
      sudo crontab -e
      ```
    - Thêm lệnh:
      ```
      35 0 * * * /usr/local/bin/backup_fgt_pw.sh >> /var/log/fortinet254_4.log 2>&1
      ```
    - Kiểm tra:
      ```
      sudo crontab -l
      ```
---
## Chỉnh tạo user backup trên FortiGate:
  ![Ảnh 1](?raw=1)
  ![Ảnh 2](?raw=1)
