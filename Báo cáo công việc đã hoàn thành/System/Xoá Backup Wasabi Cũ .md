# Sửa backup Daily giữ lại 14 history
- File Script: /root/rclone-nextcloud-backup-30d-telegram.sh
- Nội dung thay đổi:
  - Đổi biến check tuổi bản ghi `RETENTION_DAYS=30` thành `RETENTION_HISTORY_COUNT=14`
  - Đổi Script xoá bản ghi cũ
    - cũ:
      ```
      #####################################
      # CLEANUP OLD VERSIONS
      #####################################
      rclone delete "$HISTORY_BASE" \
        --min-age ${RETENTION_DAYS}d \
        --rmdirs \
        --log-file "$LOG" \
        --log-level INFO || true
      ```
    - mới:
      ```
      #####################################
      # CLEANUP OLD HISTORY BACKUPS
      #####################################
      if [ "$STATUS" = "SUCCESS" ]; then
        echo "========== CLEANUP OLD HISTORY BACKUPS $(date) ==========" >> "$LOG"
        echo "Keep latest history folders: ${RETENTION_HISTORY_COUNT}" >> "$LOG"
      
        TMP_HISTORY_LIST=$(mktemp)
      
        rclone lsf "$HISTORY_BASE" --dirs-only | sed 's#/$##' | sort | \
          grep -E '^[0-9]{4}-[0-9]{2}-[0-9]{2}$' > "$TMP_HISTORY_LIST"
      
        HISTORY_COUNT=$(wc -l < "$TMP_HISTORY_LIST")
      
        if [ "$HISTORY_COUNT" -gt "$RETENTION_HISTORY_COUNT" ]; then
          DELETE_COUNT=$((HISTORY_COUNT - RETENTION_HISTORY_COUNT))
      
          head -n "$DELETE_COUNT" "$TMP_HISTORY_LIST" | while read -r OLD_HISTORY; do
            echo "[DELETE] ${HISTORY_BASE}/${OLD_HISTORY}" >> "$LOG"
      
            rclone purge "${HISTORY_BASE}/${OLD_HISTORY}" \
              --log-file "$LOG" \
              --log-level INFO
          done
        else
          echo "[INFO] History backup count ${HISTORY_COUNT}, nothing to delete." >> "$LOG"
        fi
      
        rm -f "$TMP_HISTORY_LIST"
      
        echo "========== CLEANUP HISTORY DONE $(date) ==========" >> "$LOG"
      fi
      ```
  - Sửa dòng thông báo
    - Dòng cũ: `<b>Retention:</b> ${RETENTION_DAYS} days"`
    - Dòng mới: `<b>Retention:</b> latest ${RETENTION_HISTORY_COUNT} history folders"`
   
# Sửa Backup Full giữ lại 10 bản gần nhất
- File Script: /root/rclone-nextcloud-full-backup-telegram.sh
- Nội dung thay đổi:
  - Đổi biến check tuổi bản ghi: `RETENTION_DAYS=60` thành `RETENTION_FULL_COUNT=10`
  - Thêm Script xoá backup cũ
    - Cũ: không có
    - Mới:
    ```
    #####################################
    # CLEANUP OLD FULL BACKUPS
    #####################################
    if [ "$STATUS" = "SUCCESS" ]; then
      echo "========== CLEANUP OLD FULL BACKUPS $(date) ==========" | tee -a "$LOG"
      echo "Keep latest full backups: ${RETENTION_FULL_COUNT}" | tee -a "$LOG"
    
      TMP_FULL_LIST=$(mktemp)
    
      rclone lsf "$FULL_BASE" --dirs-only | sed 's#/$##' | sort | \
        grep -E '^[0-9]{4}-[0-9]{2}-[0-9]{2}$' > "$TMP_FULL_LIST"
    
      FULL_COUNT=$(wc -l < "$TMP_FULL_LIST")
    
      if [ "$FULL_COUNT" -gt "$RETENTION_FULL_COUNT" ]; then
        DELETE_COUNT=$((FULL_COUNT - RETENTION_FULL_COUNT))
    
        head -n "$DELETE_COUNT" "$TMP_FULL_LIST" | while read -r OLD_FULL; do
          echo "[DELETE] ${FULL_BASE}/${OLD_FULL}" | tee -a "$LOG"
    
          rclone purge "${FULL_BASE}/${OLD_FULL}" \
            --log-level INFO \
            2>&1 | tee -a "$LOG"
        done
      else
        echo "[INFO] Full backup count ${FULL_COUNT}, nothing to delete." | tee -a "$LOG"
      fi
    
      rm -f "$TMP_FULL_LIST"
    
      echo "========== CLEANUP FULL DONE $(date) ==========" | tee -a "$LOG"
    fi
    ```
  - Sửa dòng thông báo tele
    - cũ: `<b>Retention:</b> ${RETENTION_DAYS} days"`
    - Mới: `<b>Retention:</b> latest ${RETENTION_FULL_COUNT} full backups"` 
