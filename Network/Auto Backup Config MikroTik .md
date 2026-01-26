# AUTO BACKUP MIKROTIK

## 1. Mục đích
Script dùng để backup MikroTik `192.168.254.2` và lưu về máy Linux theo 2 định dạng

File cấu hình export dạng text  
`.rsc.gz`

File backup nhị phân để restore nhanh  
`.backup.gz`

Sau khi chạy thành công, script sẽ
Tạo backup trên MikroTik  
Tải file về máy Linux  
Nén gzip  
Xóa file backup trên MikroTik  
Giữ lại `KEEP=14` bản gần nhất  
Gửi thông báo Telegram thành công hoặc thất bại

---

## 2. Cài đặt máy Linux
  - Cài gói:
    ```
    apt update
    apt install -y sshpass gzip curl openssh-client
    ```
  - Kiểm tra gói:
    ```
    sshpass -V
    curl --version
    scp -V
    gzip --version
    ```
  - Tạo thư mục lưu file backup: `/backup/mikrotik2`:
    ```
    mkdir -p /backup/mikrotik2
    chmod 700 /backup/mikrotik2
    ```
  - Tạo file script:
    ```
    nano /usr/local/bin/backup_mikrotik_254_2.sh
    ```
  - Script Auto Backup:
    ```
    #!/bin/bash
    set -euo pipefail
    
    # ===== DEVICE CONFIG =====
    HOST="192.168.254.2"
    MT_USER="backup"
    MT_PASS="123@123a"
    BACKUP_DIR="/backup/mikrotik2"
    KEEP=14
    # Export mode: 'compact' (ẩn secret) hoặc 'show-sensitive' (đủ mọi thứ)
    EXPORT_MODE="compact"
    # =========================
    
    # ===== TELEGRAM CONFIG =====
    BOT_TOKEN="8363452772:AAHqUxRL6wmgKjUOR1dQjOQ4r9G4ZbVx7sg"
    CHAT_ID="-5235198580"
    # ==========================
    
    mkdir -p "$BACKUP_DIR"
    TODAY="$(date +%F)"
    BASE="auto-backup-${TODAY}"
    HAS_ANY=0
    
    echo "[*] Trigger backup on $HOST for $TODAY ..."
    if [[ "$EXPORT_MODE" == "show-sensitive" ]]; then
      EXPORT_CMD="/export show-sensitive file=${BASE}"
    else
      EXPORT_CMD="/export compact file=${BASE}"
    fi
    
    sshpass -p "$MT_PASS" ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null \
      "${MT_USER}@${HOST}" "${EXPORT_CMD}; /system backup save name=${BASE}"
    
    fetch_with_retry() {
      local remote="$1" local_path="$2" tries=10
      for ((i=1;i<=tries;i++)); do
        if sshpass -p "$MT_PASS" scp -O -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null \
             "${MT_USER}@${HOST}:${remote}" "${local_path}" 2>/dev/null; then
          return 0
        fi
        sleep 2
      done
      return 1
    }
    
    echo "[*] Pull files from $HOST ..."
    if fetch_with_retry "${BASE}.rsc"    "${BACKUP_DIR}/mt_${HOST}_${TODAY}.rsc"; then
      gzip -f9 "${BACKUP_DIR}/mt_${HOST}_${TODAY}.rsc"; HAS_ANY=1
    fi
    if fetch_with_retry "${BASE}.backup" "${BACKUP_DIR}/mt_${HOST}_${TODAY}.backup"; then
      gzip -f9 "${BACKUP_DIR}/mt_${HOST}_${TODAY}.backup"; HAS_ANY=1
    fi
    
    # cleanup on router
    sshpass -p "$MT_PASS" ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null \
      "${MT_USER}@${HOST}" ":do { /file remove ${BASE}.rsc; /file remove ${BASE}.backup } on-error={}"
    
    # rotate local
    if [[ "$HAS_ANY" -eq 1 ]]; then
      ls -t ${BACKUP_DIR}/mt_${HOST}_*.rsc.gz     2>/dev/null | tail -n +$((KEEP+1)) | xargs -r rm --
      ls -t ${BACKUP_DIR}/mt_${HOST}_*.backup.gz  2>/dev/null | tail -n +$((KEEP+1)) | xargs -r rm --
      echo "[OK] Done $HOST -> $BACKUP_DIR"
      MSG="✅ MikroTik ${HOST}: backup OK lúc $(date '+%Y-%m-%d %H:%M:%S'). Lưu tại ${BACKUP_DIR}."
    else
      echo "[WARN] Không lấy được file từ $HOST."
      MSG="⚠️ MikroTik ${HOST}: backup FAIL lúc $(date '+%Y-%m-%d %H:%M:%S'). Không tải được file."
    fi
    
    # Telegram notify
    curl -s -X POST "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
         -d chat_id="${CHAT_ID}" \
         -d text="${MSG}" >/dev/null

    ```
  - Quyền chạy script:
    ```
    chmod +x /usr/local/bin/backup_mikrotik_254_2.sh
    ```
  - Test thử Script:
    ```
    /usr/local/bin/backup_mikrotik_254_2.sh
    ```
## 3. Tạo User Backup trên MikroTik
![Ảnh 1](?raw=1)

## 4. Tạo thêm bản lưu trên PC local:

