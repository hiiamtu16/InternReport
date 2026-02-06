# AUTO BACKUP DellEMC 

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
    nano /usr/local/bin/backup_dellemc.sh
    ```
  - Script Auto backup:
    ```
    #!/usr/bin/expect -f
    set timeout 30
    
    # ===== DEVICE CONFIG =====
    set HOST 172.16.5.5
    set PASS "hpa#2025"
    
    # ===== TFTP CONFIG =====
    set TFTP 172.16.5.11
    set TFTP_DIR "/backup/fortinet"
    
    # ===== BACKUP CONFIG =====
    set BACKUP_DIR "/backup/cisco_sw"
    set KEEP 14
    set TS [clock format [clock seconds] -format "%Y%m%d-%H%M"]
    set FILE "DellEMC-S4048-ON-$TS.cfg"
    set NOW [clock format [clock seconds] -format "%Y-%m-%d %H:%M:%S"]
    
    # ===== TELEGRAM CONFIG =====
    set BOT_TOKEN "8363452772:AAHqUxRL6wmgKjUOR1dQjOQ4r9G4ZbVx7sg"
    set CHAT_ID "-5235198580"
    
    # ===== FUNCTION =====
    proc send_telegram {msg} {
        exec sh -c "curl -s -X POST https://api.telegram.org/bot$::BOT_TOKEN/sendMessage \
          -d chat_id=$::CHAT_ID \
          --data-urlencode \"text=$msg\" >/dev/null"
    }
    
    # =========================
    exec mkdir -p $BACKUP_DIR
    
    # ===== TELNET BACKUP =====
    spawn telnet $HOST
    expect "Password:"
    send "$PASS\r"
    
    expect ">"
    send "enable\r"
    expect "Password:"
    send "$PASS\r"
    
    expect "#"
    send "copy running-config tftp://$TFTP/$FILE\r"
    expect "#"
    send "exit\r"
    expect eof
    
    # ===== WAIT FILE =====
    set found 0
    for {set i 0} {$i < 10} {incr i} {
        if {[file exists "$TFTP_DIR/$FILE"]} {
            set found 1
            break
        }
        sleep 1
    }
    
    if {$found == 0} {
        catch { send_telegram "❌ DellEMC $HOST backup FAIL lúc $NOW (file not found)" }
        exit 1
    }
    
    # ===== MOVE + ROTATE =====
    exec mv "$TFTP_DIR/$FILE" "$BACKUP_DIR/"
    exec sh -c "ls -t $BACKUP_DIR/DellEMC-S4048-ON-*.cfg 2>/dev/null | tail -n +[expr {$KEEP + 1}] | xargs -r rm --"
    
    # ===== TELEGRAM OK =====
    catch { send_telegram "✅ DellEMC $HOST backup OK lúc $NOW" }
    
    exit 0
    ```
  - Cấp quyền thực thi:
    ```
    chmod +x /usr/local/bin/backup_dellemc.sh
    ```
  - Thực thi lệnh:
    ```
    /usr/local/bin/backup_dellemc.sh
    ```
  - Tạo auto thực thi lệnh:
    - Mở `Crontab`:
      ```
      sudo crontab -e
      ```
    - Thêm lệnh:
      ```
      0 3 * * * /usr/local/bin/backup_dellemc_telnet.exp >> /var/log/dellemc_telnet_backup.log 2>&1
      ```
    - Kiểm tra:
      ```
      sudo crontab -l
      ```
