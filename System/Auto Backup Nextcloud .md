# BACKUP NEXTCLOUD

## Backup Daily NAS
- File backup: `/usr/local/bin/backup_daily.sh`
- Content:
  ```
  #!/bin/bash

  DATA_SRC="/mnt/nextcloud-data"
  DAILY_DIR="/mnt/nas-backup/daily"
  LOG="/var/log/nextcloud_backup_daily.log"
  
  TODAY=$(date +%F)
  TARGET="$DAILY_DIR/$TODAY"
  
  echo "===== DAILY BACKUP $(date) =====" >> $LOG
  
  # Kiểm tra mount
  if ! mountpoint -q /mnt/nas-backup; then
      echo "[ERROR] NAS not mounted!" >> $LOG
      exit 1
  fi
  
  mkdir -p "$DAILY_DIR"
  
  # Tìm bản backup hôm qua (nếu có)
  LAST_BACKUP=$(ls -1 $DAILY_DIR | sort | tail -n 1)
  
  # Nếu có bản cũ → incremental
  if [ -d "$DAILY_DIR/$LAST_BACKUP" ]; then
      rsync -avh --delete \
          --link-dest="$DAILY_DIR/$LAST_BACKUP" \
          "$DATA_SRC/" "$TARGET/" >> $LOG 2>&1
  else
      # Nếu chưa có bản cũ → full lần đầu
      rsync -avh --delete "$DATA_SRC/" "$TARGET/" >> $LOG 2>&1
  fi
  
  # Tạo symlink "latest"
  ln -sfn "$TARGET" "$DAILY_DIR/latest"
  
  echo "[OK] Daily backup completed." >> $LOG
  echo "---------------------------------" >> $LOG
  ```

## Backup Weekly NAS
- File backup: `/usr/local/bin/backup_weekly.sh`
- Content:
  ```
  #!/bin/bash

  DATA_SRC="/mnt/nextcloud-data"
  WEEKLY_DIR="/mnt/nas-backup/weekly"
  LOG="/var/log/nextcloud_backup_weekly.log"
  
  TODAY=$(date +%F)
  TARGET="$WEEKLY_DIR/full-$TODAY"
  
  echo "===== WEEKLY FULL BACKUP $(date) =====" >> $LOG
  
  # Kiểm tra mount
  if ! mountpoint -q /mnt/nas-backup; then
      echo "[ERROR] NAS not mounted!" >> $LOG
      exit 1
  fi
  
  mkdir -p "$WEEKLY_DIR"
  
  # Backup FULL (không dùng hardlink)
  rsync -avh --delete "$DATA_SRC/" "$TARGET/" >> $LOG 2>&1
  
  echo "[OK] Weekly full backup completed." >> $LOG
  echo "---------------------------------" >> $LOG
  ```

## Backup Monthly Wasabi
- File thông tin BOT: `/etc/hpa-backup.env`
- Content:
  ```
  BOT_TOKEN="8363452772:AAHqUxRL6wmgKjUOR1dQjOQ4r9G4ZbVx7sg"
  CHAT_ID="-5228743167"
  ```
- File backup: `/root/rclone-nextcloud-backup-30d-telegram.sh`
- Content:
  ```
  #!/bin/bash
  set -euo pipefail
  
  #####################################
  # LOAD TELEGRAM ENV
  #####################################
  source /etc/hpa-backup.env
  
  #####################################
  # CONFIG
  #####################################
  SRC="/mnt/nextcloud-data"
  DEST="wasabi:cloud-data/nextcloud/current"
  HISTORY_BASE="wasabi:cloud-data/nextcloud/history"
  HISTORY_TODAY="${HISTORY_BASE}/$(date +%F)"
  LOG="/var/log/rclone-nextcloud-backup.log"
  RETENTION_DAYS=30
  HOSTNAME=$(hostname)
  
  #####################################
  # FUNCTIONS
  #####################################
  send_telegram() {
    local MSG="$1"
    curl -s -X POST "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
      -d chat_id="${CHAT_ID}" \
      -d parse_mode="HTML" \
      -d text="${MSG}" >/dev/null
  }
  
  #####################################
  # START
  #####################################
  START_TIME=$(date +%s)
  
  send_telegram "🚀 <b>Nextcloud Backup START</b>
  <b>Host:</b> ${HOSTNAME}
  <b>Time:</b> $(date)"
  
  #####################################
  # BACKUP
  #####################################
  if rclone sync "$SRC" "$DEST" \
    --backup-dir "$HISTORY_TODAY" \
    --transfers 12 \
    --checkers 24 \
    --s3-upload-concurrency 12 \
    --s3-chunk-size 128M \
    --fast-list \
    --no-traverse \
    --s3-disable-checksum \
    --log-file "$LOG" \
    --log-level INFO
  then
    STATUS="SUCCESS"
  else
    STATUS="FAILED"
  fi
  
  #####################################
  # CLEANUP OLD VERSIONS
  #####################################
  rclone delete "$HISTORY_BASE" \
    --min-age ${RETENTION_DAYS}d \
    --rmdirs \
    --log-file "$LOG" \
    --log-level INFO || true
  
  #####################################
  # FINISH
  #####################################
  END_TIME=$(date +%s)
  DURATION=$((END_TIME - START_TIME))
  DURATION_FMT=$(printf '%02dh:%02dm:%02ds' $((DURATION/3600)) $((DURATION%3600/60)) $((DURATION%60)))
  
  SIZE=$(rclone size "$DEST" 2>/dev/null | grep "Total size:" | sed 's/Total size: //')
  
  if [ "$STATUS" = "SUCCESS" ]; then
    send_telegram "✅ <b>Nextcloud Backup SUCCESS</b>
  <b>Host:</b> ${HOSTNAME}
  <b>Duration:</b> ${DURATION_FMT}
  <b>Data size:</b> ${SIZE}
  <b>Retention:</b> ${RETENTION_DAYS} days"
  else
    send_telegram "❌ <b>Nextcloud Backup FAILED</b>
  <b>Host:</b> ${HOSTNAME}
  <b>Duration:</b> ${DURATION_FMT}
  <b>Log:</b> ${LOG}"
    exit 1
  fi
  ```

## Backup Full Wasabi
- File backup: `/root/rclone-nextcloud-full-backup-telegram.sh`
- Content:
  ```
  #!/bin/bash
  set -euo pipefail
  
  #####################################
  # LOAD TELEGRAM ENV
  #####################################
  source /etc/hpa-backup.env
  
  #####################################
  # CONFIG
  #####################################
  SRC="/mnt/nextcloud-data"
  FULL_BASE="wasabi:cloud-data/nextcloud/full"
  FULL_TODAY="${FULL_BASE}/$(date +%F)"
  LOG="/var/log/rclone-nextcloud-full-backup.log"
  RETENTION_DAYS=60
  HOSTNAME=$(hostname)
  
  #####################################
  # FUNCTIONS
  #####################################
  send_telegram() {
    local MSG="$1"
    curl -s -X POST "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
      -d chat_id="${CHAT_ID}" \
      -d parse_mode="HTML" \
      -d text="${MSG}" >/dev/null
  }
  
  #####################################
  # START
  #####################################
  START_TIME=$(date +%s)
  
  send_telegram "📦 <b>Nextcloud FULL Backup START</b>
  <b>Host:</b> ${HOSTNAME}
  <b>Time:</b> $(date)
  <b>Target:</b> ${FULL_TODAY}"
  
  echo "========== START FULL BACKUP $(date) ==========" | tee -a "$LOG"
  echo "Host: ${HOSTNAME}" | tee -a "$LOG"
  echo "Source: ${SRC}" | tee -a "$LOG"
  echo "Target: ${FULL_TODAY}" | tee -a "$LOG"
  echo "==============================================" | tee -a "$LOG"
  
  #####################################
  # FULL BACKUP
  #####################################
  if rclone sync "$SRC" "$FULL_TODAY" \
    -I \
    --exclude "/appdata_*/dav-photocache/**" \
    --exclude "/**/files_versions/**" \
    --exclude "/**/onlyoffice/**" \
    --transfers 12 \
    --checkers 24 \
    --retries 5 \
    --low-level-retries 20 \
    --s3-upload-concurrency 12 \
    --s3-chunk-size 128M \
    --fast-list \
    --s3-disable-checksum \
    --log-level INFO 2>&1 | tee -a "$LOG"
  then
    STATUS="SUCCESS"
  else
    STATUS="FAILED"
  fi
  
  #####################################
  # CLEANUP OLD FULL BACKUPS
  #####################################
  echo "========== CLEANUP OLD BACKUPS $(date) ==========" | tee -a "$LOG"
  
  rclone delete "$FULL_BASE" \
    --min-age ${RETENTION_DAYS}d \
    --rmdirs \
    --log-level INFO 2>&1 | tee -a "$LOG" || true
  
  #####################################
  # FINISH
  #####################################
  END_TIME=$(date +%s)
  DURATION=$((END_TIME - START_TIME))
  DURATION_FMT=$(printf '%02dh:%02dm:%02ds' \
    $((DURATION/3600)) $((DURATION%3600/60)) $((DURATION%60)))
  
  SIZE=$(rclone size "$FULL_TODAY" 2>/dev/null | grep "Total size:" | sed 's/Total size: //' || echo "N/A")
  
  echo "========== FINISH $(date) ==========" | tee -a "$LOG"
  echo "STATUS: ${STATUS}" | tee -a "$LOG"
  echo "DURATION: ${DURATION_FMT}" | tee -a "$LOG"
  echo "SIZE: ${SIZE}" | tee -a "$LOG"
  echo "LOG: ${LOG}" | tee -a "$LOG"
  echo "====================================" | tee -a "$LOG"
  
  if [ "$STATUS" = "SUCCESS" ]; then
    send_telegram "✅ <b>Nextcloud FULL Backup SUCCESS</b>
  <b>Host:</b> ${HOSTNAME}
  <b>Duration:</b> ${DURATION_FMT}
  <b>Data size:</b> ${SIZE}
  <b>Retention:</b> ${RETENTION_DAYS} days"
  else
    send_telegram "❌ <b>Nextcloud FULL Backup FAILED</b>
  <b>Host:</b> ${HOSTNAME}
  <b>Duration:</b> ${DURATION_FMT}
  <b>Log:</b> ${LOG}"
    exit 1
  fi
  ```
