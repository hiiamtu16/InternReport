# GHI ÂM CUỘC GỌI VÀ BIND MOUNT SANG NAS

## Bật ghi âm cuộc gọi
- Bật Trunk record
- Bật Inbound Route record
- Bật Outbound Route record
- Bật Record Extension
  ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/a86134017d54539224af85a5cc9b3a3b44944c90/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/VoIP/Record%20voip.apps.vn/1.png?raw=1)

## Tạo Shared Folder trên NAS
- Server NAS:
  ```https://172.16.6.10:5443/tos/#```
- Vị trí lưu: `Volume 1/Record-VoIP/`

## Chạy lệnh
- Xác định nơi lưu file record:
```
grep -E 'astspooldir|astvarlibdir' /etc/asterisk/asterisk.conf
find /var/spool/asterisk -maxdepth 2 -type d \( -name monitor -o -name recording \) 2>/dev/null
find /var/spool/asterisk -type f \( -iname '*.wav' -o -iname '*.WAV' -o -iname '*.mp3' -o -iname '*.gsm' \) | head -n 50
```
- Xem dung lượng file record:
```
du -sh /var/spool/asterisk/monitor 2>/dev/null
du -sh /var/spool/asterisk/recording 2>/dev/null
```
- Cài NFS
```
apt update
apt install -y nfs-common
```
- Tạo điểm mount
```
mkdir -p /mnt/record-voip
```
- Mount:
```
mount -t nfs 172.16.6.10:/Volume1/Record-VoIP /mnt/record-voip
```
- Kiểm tra mount:
```
df -h | grep record-voip
mount | grep record-voip
touch /mnt/record-voip/test_write.txt
ls -l /mnt/record-voip
```
- Mount tự động sau reboot
  - Mở file:
   ```
   nano /etc/fstab
   ```
  - Thêm dòng
   ```
   172.16.6.10:/Volume1/Record-VoIP /mnt/record-voip nfs defaults,_netdev,noatime 0 0
   ```
  - Kiểm tra:
   ```
   mount -a
   ```
- Chuyển dữ liệu record của `Asterisk` sang `NAS`
  - Dừng Asterisk
   ```
   systemctl stop asterisk
   ```
  - Tạo thư mục trên NAS để chứa record
   ```
   mkdir -p /mnt/record-voip/monitor
   ```
  - Backup thư mục cũ
   ```
   mv /var/spool/asterisk/monitor /var/spool/asterisk/monitor_bak_$(date +%F)
   mkdir -p /var/spool/asterisk/monitor
   ```
  - Copy dữ liệu cũ sang NAS
   ```
   LATEST=$(ls -dt /var/spool/asterisk/monitor_bak_* | head -n 1)
   cp -a "$LATEST"/. /mnt/record-voip/monitor/
   ```
  - Gán quyền
    - Xem Asterisk chạy bằng user nào:
     ```
     ps -ef | grep asterisk
     id asterisk
     ```
    - Nếu là user `asterisk` thì chạy
     ```
     chown -R asterisk:asterisk /mnt/record-voip/monitor
     chmod -R 775 /mnt/record-voip/monitor
     ```
  - Bind mount NAS vào đường dẫn cũ
   ```
   mount --bind /mnt/record-voip/monitor /var/spool/asterisk/monitor
   ```
  - Kiểm tra:
   ```
   mount | grep /var/spool/asterisk/monitor
   df -h | grep record-voip
   ```
  - Thêm bind mount vào `/etc/fstab`
    - Mở:
     ```
     nano /etc/fstab
     ```
    - Thêm dòng vào dưới
     ```
     /mnt/record-voip/monitor /var/spool/asterisk/monitor none bind 0 0
     ```
  - Khởi động lại `Asterisk`
  ```
  systemctl start asterisk
  systemctl status asterisk
  ```

## Kết quả
  ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/a86134017d54539224af85a5cc9b3a3b44944c90/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/VoIP/Record%20voip.apps.vn/2.png?raw=1)

## Nghe ghi âm trực tiếp trên web HAPBX
- Xác định user đang chạy trên web
```
ps -ef | egrep 'apache2|httpd|nginx|php-fpm' | grep -v grep
```
- Nếu user `www-data`, test
  - Cho user vào gr `asterisk`
   ```
   usermod -aG asterisk www-data
   ```
  - Cho toàn bộ file record hiện tại đọc được theo group
   ```
   find /var/spool/asterisk/monitor -type d -exec chmod 775 {} \;
   find /var/spool/asterisk/monitor -type f -exec chmod 664 {} \;
   ```
  - Restart nginx và php fpm
   ```
   systemctl restart nginx
   systemctl restart php8.2-fpm
   ```
  - Cho các thư mục sau này vẫn nghe đc
    - Script
     ```
      cat > /usr/local/bin/fix-monitor-perms.sh <<'EOF'
      #!/bin/bash
      find /var/spool/asterisk/monitor -type f -name '*.wav' ! -perm 664 -exec chmod 664 {} \;
      find /var/spool/asterisk/monitor -type d ! -perm 775 -exec chmod 775 {} \;
      EOF
      chmod +x /usr/local/bin/fix-monitor-perms.sh
     ``` 
    - Chạy crontab
      - Mở `crontab`
       ```
       crontab -e
       ```
      - Thêm dòng:
       ```
       * * * * * /usr/local/bin/fix-monitor-perms.sh
       ```

