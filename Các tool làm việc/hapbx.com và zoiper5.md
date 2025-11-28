HAPBX.com
Đăng nhập bằng tài khoản google vào trang "https://billing.hapbx.com/clientarea.php"
Chuyển đến cửa sổ "https://billing.hapbx.com/clientarea.php"
Mua dịch vụ mới:
  Bấm "Order New Services" ở góc dưới bên trái
  Bấm "Select" vào dịch vụ cần mua
  Bấm "Checkout"
  Check thông tin và bấm tiếp "Checkout"
  Đợi thanh toán và bấm "Continue To Client Area"
Xem thông tin dịch vụ vừa mua:
  Bấm "View Details" vào dịch vụ vừa mua (dịch vụ mua mới nhất ở trên cùng)
  Bấm "Start", đợi kích hoạt dịch vụ từ thiết bị quản lý
  Kiểm tra Status Running ==> ok
  Kéo xuống cuối lấy IP Address paste vào google
Trên Web dịch vụ:
  Đăng nhập lần đầu với Username mặc định "Admin", tự tạo pass cho Admin
  Set Extentions:
    Cột bên trái chọn PBX --> Extentions --> Extentions
    Điền các thông tin:
      Extension: 224
      Name: Logiclab Thuy Khue 224
      Features Password: *44483
      Internal CID: Logiclab Thuy Khue 224    -    224
      Devices:
        Technology: PJSIP
        User Device: 224
        Password: 59xuandieu
        Device Description: Logiclab Thuy Khue 224
        Ring device: tick "Yes"
        Còn lại để mặc định
        Bấm "Save" (nút màu xanh góc dưới bên phải) 
        Bấm "Reload" (nút màu đỏ nhấp nháy góc trên bên phải)
        Bấm nút 3 đường gạch ngang góc trên bên phải chọn extention vừa save (224) rồi bấm "Update" góc dưới bên phải
    Set Trunks:
      Cột bên trái chọn PBX --> Calls Routing --> Trunks
      Điền các thông tin:
        Technology: PJSIP
        Description: VTC_842444558688
        Codecs: ulaw, alaw, g729, g722
        Calls Recording: tick Yes
        General Configurations
          Local Username: 842444558688
          Remote Host: 119.18.190.65
          Remote Port: 5060
          Local Secret: 2104@2025
          Match: 119.18.190.65
          Remote Username: 842444558688
          Remote Secret: 2104@2025
          From User: 842444558688
          From Domain: 119.18.190.65
        Outbound Registration Settings
          Require Registration: Tick Yes (mỗi lần chỉ có 1 IP duy nhất bật, test nhiều IP phải nhớ tắt IP đã test xong)
          Client URI: sip:842444558688@119.18.190.65
          Server URI: sip:119.18.190.65
          Contact User: 842444558688
        Còn lại để mặc định
        Bấm "Save" (nút màu xanh góc dưới bên phải) 
        Bấm "Reload" (nút màu đỏ nhấp nháy góc trên bên phải)
        Bấm nút 3 đường gạch ngang góc trên bên phải chọn extention vừa save (224) rồi bấm "Update" góc dưới bên phải
    Set Outbound Routes:
      Cột bên trái chọn PBX --> Calls Routing --> Outbound Routes
      
        
