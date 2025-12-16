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
    ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/1.png?raw=1)
    ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/2.png?raw=1)
    ![Ảnh 5](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/5.png?raw=1)
    ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/3.png?raw=1)
    ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/4.png?raw=1)
  * Cài đặt
    ![Ảnh 6](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/6.png?raw=1)
    ![Ảnh 7](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/7.png?raw=1)
    ![Ảnh 8](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/8.png?raw=1)
    ![Ảnh 9](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/9.png?raw=1)
    ![Ảnh 10](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/10.png?raw=1)
  * Kết quả:
    ![Ảnh 11](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/11.png?raw=1)
    ![Ảnh 12](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/12.png?raw=1)
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
    - ![Ảnh 13](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/13.png?raw=1)
## Cấu hình Network
  ### Dạng Flat
   * Đặt IP VM cùng dải, cùng gateway với IP máy thật
  ### Multi VLAN
   * Đặt IP các VM thuộc các VLAN có dải IP khác nhau
## Tạo VM, Move disk VM giữa các Data Storage
 ### Tạo VM
  * Upload file iso:
    ![Ảnh 15](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/15.png?raw=1)
  * Tạo VM
    ![Ảnh 14](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/14.png?raw=1)
    ![Ảnh 16](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/16.png?raw=1)
    ![Ảnh 17](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/17.png?raw=1)
    - Kiểm tra boot order
    ![Ảnh 18](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/18.png?raw=1)
  * Xem máy ảo
    ![Ảnh 19](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/19.png?raw=1)
 ### Move disk
  * Trên máy Proxmox VE, add thêm 1 ổ cứng (sdc 60GB) như các bước trên
  * Thao tác move disk
    ![Ảnh 20](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/20.png?raw=1)
  * Kiểm tra:
    - Ổ sdb sau khi move ![Ảnh 21](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/21.png?raw=1)
    - Ổ sdc sau khi move ![Ảnh 22](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/22.png?raw=1)
    - VM vẫn hoạt động được sau khi move ![Ảnh 23](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/23.png?raw=1)

## Back up / restore VM
 ### Back up
  * Back up trên 1 máy: 
    - Back up: ![Ảnh 24](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/24.png?raw=1)
    - Kiểm tra: ![Ảnh 25](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/25.png?raw=1)
  * Back up tự động nhiều máy
     - Back up:
     ![Ảnh 26](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/26.png?raw=1)
     ![Ảnh 27](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/27.png?raw=1)
 ### Restore
   ![Ảnh 28](https://github.com/hiiamtu16/InternReport/blob/6f6630018d2e9bd17f13a4f3dcaa23d3caaa50b2/Picture%20/Virtualization/Proxmox%209.1/28.png?raw=1)
