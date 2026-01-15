## Tạo Remote Backup trên PBS
  - Tạo thư mục lưu:
    ```bash
    sudo mkdir -p /mnt/datastore/clone-vm/
    ```
  - Cấp quyền:
    ```bash
    sudo chown -R www-data:www-data /mnt/datastore/
    ```
  - Backing Path copy vào PBS: ```/mnt/datastore/clone-vm/```
  - Lấy IP Public của PBS:
    ```bash
    curl ifconfig.me
    ```
  - Tạo remote:   
    ![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/18.png?raw=1)
  - Tạo Pull Sync job trong DataStore trên PBS và **run now** để sync.
    ![Ảnh 22](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/18.png?raw=1)
    ![Ảnh 23](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/18.png?raw=1)

## Tạo Backup Wasabi cho nhiều PBS cùng Backup & Restore
  - Back up:
    - Tạo DataStore trên PBS:
    - Tạo S3 endpoint chung để backup & restore  
    ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/12.png?raw=1)
    - Tạo Local Cache (mở SSH PBS):
      ```bash
      mkdir -p /var/lib/proxmox-backup/wasabi/clone-checkmk
      chown -R backup:backup /var/lib/proxmox-backup
      chmod 700 /var/lib/proxmox-backup
      ```
    - Copy đường dẫn cho Local Cache: `/var/lib/proxmox-backup/wasabi/clone-checkmk`  
    ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/13.png?raw=1)
    - Link PVE vs PBS:  
    ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/14.png?raw=1)  
    ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/15.png?raw=1)
    - Back up:   
    ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/16.png?raw=1)
  - Restore:
    - Xoá `.in-use` trên Wasabi bucket  
    ![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/49906d15f7361a434e45365a68800950d565df10/Picture%20/Virtualization/T%E1%BA%A1o%20Clone%20CheckMK%20tr%C3%AAn%20Ubuntu/17.png?raw=1)
    - Add S3 endpoint và tạo DataStore trên PVE muốn dùng để restore (như trên).
    - Restore dữ liệu về (Doc: [Restore guide](https://github.com/hiiamtu16/InternReport/blob/40ea60981f1a60c4da1851c30288b50424e31952/Virtualization/Restore%20with%20RClone%20Wasabi.md))
    - Đổi IP tĩnh.
    - Đổi hostname:
      - Đổi hostname trong 2 file:
        - ```sudo nano /etc/hostname```
        - ```sudo nano /etc/hosts```
      - Reboot: ```sudo reboot```










