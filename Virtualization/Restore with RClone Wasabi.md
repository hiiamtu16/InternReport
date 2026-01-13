# RCLONE

* Kiểm tra server Wasabi Backup:
  ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/8f8934a0a06d4a89fc4a279074976687cf07e1f8/Picture%20/Virtualization/Restore%20Wasabi/1.png?raw=1)
* Cài RClone trên máy Ubuntu: curl https://rclone.org/install.sh | sudo bash
* Kiểm tra: rclone version
* Tạo Remote Version Wasabi trong RClone:
  - Chạy cấu hình: rclone config
  - Chọn tạo remote mới: n
  - Đặt tên remote: RestoreProxmox
  - Chọn Storage: s3
  - Chọn Provider: Wasabi
  - env_auth: 1 ( hoặc false)
  - access_key: SGT4VP3G7DNROXKUTNCC
  - secret_key: LPhv8gOpmkX8TEE0z5rnNaIhUwHAChBjUeio2ZDj
  - region: Enter
  - endpoint: chọn server giống với Server Wasabi Backup (singapore)
  - location constraint: ap-southeast-1 (đúng với backup Server Wasabi)
  - acl: Enter
  - Edit advanced config: no
  - Keep this "..." remote: yes
  - quit
* Kiểm tra kết nối (xem list bucket, nếu có là cấu hình đúng): rclone lsd RestoreProxmox:
* Tạo thư mục restore trên máy: mkdir -p /restore/backup-cuongnv (đường dẫn thư mục)
* Copy dữ liệu từ Wasabi về local: rclone copy RestoreProxmox:backup-cuongnv /restore/backup-cuongnv --progress
* ( rclone copy 'tên remote mình vừa tạo':'tên bucket trên wasabi' 'đường dẫn mình vừa tạo' --progress
  ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/8f8934a0a06d4a89fc4a279074976687cf07e1f8/Picture%20/Virtualization/Restore%20Wasabi/2.png?raw=1)

