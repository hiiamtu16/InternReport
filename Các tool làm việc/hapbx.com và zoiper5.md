# Hướng Dẫn Thiết Lập Và Cấu Hình HAPBX

## 1. Đăng Nhập Và Mua Dịch Vụ

### Đăng nhập vào HAPBX  
Truy cập trang  
https://billing.hapbx.com/clientarea.php  
Đăng nhập bằng tài khoản Google.

### Mua dịch vụ mới  
Bấm **Order New Services**  
![Order New Services](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Order%20New%20Services.png)

Bấm **Select** vào dịch vụ cần mua  
![Select Service](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Select%20Service.png)

Chọn server đúng khu vực rồi bấm **Checkout**  
![Checkout 1](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Checkout%201.png)

Kiểm tra thông tin và bấm tiếp **Checkout**  
![Checkout 2](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Checkout%202.png)

Đợi thanh toán xong và bấm **Continue To Client Area**.

---

## 2. Xem Thông Tin Và Kích Hoạt Dịch Vụ

Bấm **View Details** vào dịch vụ vừa mua  
![View Details](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/ViewDetails.png)

Bấm **Start**, chờ hệ thống kích hoạt.

Kiểm tra trạng thái **Running** → OK.

Kéo xuống cuối trang lấy **IP Address**  
![Check IP](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/CheckDetails.png)

Paste IP vào trình duyệt để truy cập trang quản trị.

---

## 3. Đăng Nhập Trên Web Dịch Vụ

Lần đầu đăng nhập  
![Login Admin](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/login.png)

Username mặc định  

Tự tạo mật khẩu mới.

---

## 4. Thiết Lập Extentions

### Mở mục Extentions  
PBX → Extentions → Extentions  
![Extensions](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Extension.png)

### Cấu hình Extention  
Extension: 224
Name: Logiclab Thuy Khue 224
Features Password: *44483
Internal CID: Logiclab Thuy Khue 224 - 224


### Cấu hình Devices  
Technology: PJSIP
User Device: 224
Password: 59xuandieu
Device Description: Logiclab Thuy Khue 224
Ring device: Yes


### Lưu cấu hình  
Bấm **Save**  
Bấm **Reload**  
Chọn extention vừa tạo và bấm **Update**

![Choose Extention](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/ChooseExtention.png)

![Extention Updated](https://github.com/hiiamtu16/InternReport/blob/f41be1d5f61c6dad4cb4072834e3cf79184d6258/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Extension2.png)

---

## 5. Thiết Lập Trunks

### Mở mục Trunks  
PBX → Calls Routing → Trunks  
![Trunks](https://github.com/hiiamtu16/InternReport/blob/cabb5a80f012dde1cb5718fadbb031e24edc3722/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Trunk1.png)

### Cấu hình Trunk  
Technology: PJSIP
Description: VTC_842444558688
Codecs: ulaw, alaw, g729, g722
Calls Recording: Yes

### General Configurations  
Local Username: 842444558688
Remote Host: 119.18.190.65
Remote Port: 5060
Local Secret: 2104@2025
Match: 119.18.190.65
Remote Username: 842444558688
Remote Secret: 2104@2025
From User: 842444558688
From Domain: 119.18.190.65

### Outbound Registration Settings  
![Trunk Registration](https://github.com/hiiamtu16/InternReport/blob/86d047c7e267ca1298853b3f515672793ba05d14/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Trunk2.png)

Require Registration: Yes
Client URI: sip:842444558688@119.18.190.65
Server URI: sip:119.18.190.65
Contact User: 842444558688


Còn lại để mặc định.

### Lưu cấu hình  
Bấm **Save**  
Bấm **Reload**  
Chọn trunk đã tạo rồi bấm **Update**

![Choose Trunk](https://github.com/hiiamtu16/InternReport/blob/86d047c7e267ca1298853b3f515672793ba05d14/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/ChooseTrunk.png)

---

## 6. Thiết Lập Outbound Routes

PBX → Calls Routing → Outbound Routes  
![Outbound Routes](https://github.com/hiiamtu16/InternReport/blob/8de83856433c2fd5272f1c8484da811cb9e0f301/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/Outbound1.png)

### Cấu hình Outbound  
Description: salevt
Trunk: chọn trunk vừa tạo
Import file CSV

Bấm **Save** và **Reload**

Chọn Outbound vừa tạo và bấm **Update**.

---

## 7. Kiểm Tra Lại Cấu Hình

Kiểm tra lại Extention, Trunk và Outbound để đảm bảo đúng.

Kiểm tra Dashboard  
![Dashboard](https://github.com/hiiamtu16/InternReport/blob/48340a94c1517240424f5b0fe1eea14894baa449/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/checkDashboard.png)

Đối chiếu thông số dịch vụ với thông tin trên web.

---

## 8. Cấu Hình Zoiper5

### Đăng nhập Zoiper  
![Zoiper Login](https://github.com/hiiamtu16/InternReport/blob/ffa544d8dc72d14d6423497551e5020cf1532818/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/loginZoiper5.png)

Username: 224
Password: 59xuandieu

Nhập IP Address của dịch vụ  
![Zoiper Enter IP](https://github.com/hiiamtu16/InternReport/blob/ffa544d8dc72d14d6423497551e5020cf1532818/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/loginZoiper5_2.png)

Bấm **Skip** → chờ thiết bị hoạt động → bấm **Next**  
![Zoiper Next](https://github.com/hiiamtu16/InternReport/blob/ffa544d8dc72d14d6423497551e5020cf1532818/Picture%20/C%C3%A1c%20tool%20l%C3%A0m%20vi%E1%BB%87c/loginZoiper5_3.png)

### Test cuộc gọi  
Gọi thử để xác nhận hoạt động bình thường.

---

