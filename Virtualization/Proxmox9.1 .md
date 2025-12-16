# PROXMOX 9.1

## Cài đặt Proxmox
  * Tải Proxmox VE: https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso
  * Tắt Hyper-V features:
    - Control Panel → Programs → Turn Windows features on or off, tắt các mục:
       + Hyper-V
       + Virtual Machine Platform
       + Windows Hypervisor Platform
       + Windows Subsystem for Linux
       + Windows Sandbox
    - Tắt hypervisor ở mức boot: Mở CMD (Run as Administrator): bcdedit /set hypervisorlaunchtype off
    - Tắt Memory Integrity: Windows Security → Device Security → Core Isolation → Memory Integrity = OFF
    - Restart máy
  * Cấu hình máy trên VMware Workstation:
    ![Ảnh 1](?raw=1)
    ![Ảnh 2](?raw=1)
    ![Ảnh 5](?raw=1)
    ![Ảnh 3](?raw=1)
    ![Ảnh 4](?raw=1)
  * Cài đặt
    ![Ảnh 6](?raw=1)
    ![Ảnh 7](?raw=1)
    ![Ảnh 8](?raw=1)
    ![Ảnh 9](?raw=1)
    ![Ảnh 10](?raw=1)
  * Kết quả:
    ![Ảnh 11](?raw=1)
    ![Ảnh 12](?raw=1)
## Add Storage
  * Tắt máy Proxmox VE
  * Add thêm ổ cứng vào Proxmox VE
  * Kiểm tra thiết bị nhận ổ cứng chưa: lsblk (có ổ dev/sdb)
  * Chạy lệnh trên Proxmox
    - Tạo Physical Volume: pvcreate /dev/sdb
    - kiểm tra: pvs
    - Tạo Volume Group mới: vgcreate vg_sdb /dev/sdb
    - Kiểm tra: vgs
    - Tạo Thin Pool: lvcreate -l 100%FREE -T vg_sdb/data
    - Kiểm tra: lvs
  * Add Storage trên web gui:
    - ![Ảnh 13](?raw=1)
## Cấu hình Network
  ### Dạng Flat
   * Đặt IP VM cùng dải, cùng gateway với IP máy thật
  ### Multi VLAN
   * Đặt IP các VM thuộc các VLAN có dải IP khác nhau
## Tạo VM, Move disk VM giữa các Data Storage
 ### Tạo VM
  * Upload file iso:
    ![Ảnh 15](?raw=1)
  * Tạo VM
    ![Ảnh 14](?raw=1)
    ![Ảnh 16](?raw=1)
    ![Ảnh 17](?raw=1)
    - Kiểm tra boot order
    ![Ảnh 18](?raw=1)
  * Xem máy ảo
    ![Ảnh 19](?raw=1)
 ### Move disk
  * Trên máy Proxmox VE, add thêm 1 ổ cứng (sdc 60GB) như các bước trên
  * Thao tác move disk
    ![Ảnh 20](?raw=1)
  * Kiểm tra:
    - Ổ sdb sau khi move ![Ảnh 21](?raw=1)
    - Ổ sdc sau khi move ![Ảnh 22](?raw=1)
    - VM vẫn hoạt động được sau khi move ![Ảnh 23](?raw=1)

## Back up / restore VM
 ### Back up
  * Back up trên 1 máy: 
    - Back up: ![Ảnh 24](?raw=1)
    - Kiểm tra: ![Ảnh 25](?raw=1)
  * Back up tự động nhiều máy
     - Back up:
     ![Ảnh 26](?raw=1)
     ![Ảnh 27](?raw=1)
 ### Restore
   ![Ảnh 28](?raw=1)
