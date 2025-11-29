# Hướng Dẫn Cài Đặt RAID 5 Trên Máy Chủ Dell PowerEdge R620

RAID 5 là một cấu hình RAID phổ biến cung cấp khả năng dự phòng dữ liệu và hiệu suất đọc tốt. Hướng dẫn này sẽ giúp bạn cài đặt RAID 5 trên máy chủ Dell PowerEdge R620.

## Bước 1: Truy Cập Dell PERC

1. **Khởi Động và Truy Cập PERC**:
   - Khi máy chủ khởi động, bạn sẽ thấy thông báo của Dell PERC. Ngay khi máy chủ bắt đầu khởi động (trước khi vào hệ điều hành), nhấn phím **Ctrl + R** để vào giao diện cấu hình RAID.

   ![Dell PERC Screen](https://github.com/cuongnvvietis/NhanHoa/blob/main/Docs/Esxi/Picture/Raid/Screenshot_6.png)

## Bước 2: Tạo RAID 5

1. **Chọn Create Virtual Disk**:
   - Trong giao diện Dell PERC, chọn **Create Virtual Disk** để bắt đầu cấu hình RAID.


2. **Chọn RAID Level**:
   - Trong màn hình tiếp theo, chọn **RAID 5** từ danh sách các cấp RAID.

3. **Chọn Các Ổ Đĩa**:
   - Chọn ít nhất ba ổ đĩa mà bạn muốn sử dụng cho RAID 5. Đảm bảo các ổ đĩa đã được kết nối và nhận diện bởi PERC.

   ![Dell PERC Screen](https://github.com/cuongnvvietis/NhanHoa/blob/main/Docs/Esxi/Picture/Raid/Screenshot_7.png)

4. **Xác Nhận Cấu Hình**:
   - Kiểm tra lại cấu hình RAID 5 của bạn và chọn **OK** để xác nhận và tạo RAID 5.

   ![Dell PERC Screen](https://github.com/cuongnvvietis/NhanHoa/blob/main/Docs/Esxi/Picture/Raid/Screenshot_8.png)

## Bước 3: Lưu Cài Đặt và Khởi Động Lại Máy Chủ

1. **Lưu Cài Đặt**:
   - Sau khi cấu hình xong, chọn **Save** và **Exit** để lưu các thay đổi.

2. **Khởi Động Lại Máy Chủ**:
   - Khởi động lại máy chủ để các thay đổi có hiệu lực.

