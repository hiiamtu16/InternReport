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
## Cấu hình Network
  ### Dạng Flat

  ### Multi VLAN

## Tạo VM, Move disk VM giữa các Data Storage

## Back up / restore VM
