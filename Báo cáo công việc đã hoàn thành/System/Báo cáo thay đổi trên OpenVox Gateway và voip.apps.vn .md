# QUY HOẠCH LẠI SIM GỌI RA TRÊN OPENVOX VÀ GỘP TRUNK TRÊN VOIP.APPS.VN

# Quy hoạch lại sim gọi ra
- Chia Group Sim:
  - Sim 1 - 20: Viettel - grp-AllViettel
  - Sim 21 - 35: Vina   - grp-AllVina
  - Sim 36 - 40: Mobi   - grp-AllMMobi

- Sip Endpoint:
  - sip-AllViettel1 :
    - Dành cho cuộc gọi đến đầu số Viettel
    - Sử dụng cấu hình cũ không thay đổi từ System Admin Trần Văn Quyền
  - sip-AllVina
    - Tạo mới Sip AllVina
  - sip-AllMobi
    - Tạo mới Sip AllMobi

- Call Routing Rules
  - RouteViettel
    - Sử dụng cấu hình cũ không thay đổi từ System Admin Trần Văn Quyền
    - From: sip-AllViettel1
    - To: grp-AllViettel
  - RouteVina
    - Tạo mới cấu hình RouteVina
    - From: sip-AllVina
    - To: grp-AllVina
  - RouteMobi
    - Tạo mới cấu hình RouteMobi
    - From: sip-AllMobi
    - To: grp-AllMobi

## Gộp trunk trên voip.apps.vn
- Trunk:
  - AllViettel1:
    - Sử dụng cấu hình trunk cũ không thay đổi từ System Admin Trần Văn Quyền
  - AllVina:
    - Tạo mới cấu hình Trunk AllVina cho các cuộc gọi ra sim Vina
  - AllMobi:
    - Tạo mới cấu hình Trunk AllMobi cho các cuộc gọi ra sim Mobi

- Outbound:
  - RouteViettel:
    - Sử dụng cấu hình outbound cũ không thay đổi từ System Admin Trần Văn Quyền
  - RouteVina:
    - Tạo mới cấu hình RouteVina, gộp các đầu số Vina từ cấu hình cũ vào
    - Các đầu Vina đang sử dụng:
      - 081XXXXXXX
      - 082XXXXXXX
      - 083XXXXXXX
      - 084XXXXXXX
      - 085XXXXXXX
      - 087XXXXXXX
      - 088XXXXXXX
      - 091XXXXXXX
      - 094XXXXXXX
  - RouteMobi:
    - Rạo mới cấu hình RouteMobi, gộp các đầu số Mobi từ cấu hình cũ vào
    - Các đầu số Mobi đang sử dụng:
      - 070XXXXXXX
      - 076XXXXXXX
      - 077XXXXXXX
      - 078XXXXXXX
      - 079XXXXXXX
      - 089XXXXXXX
      - 090XXXXXXX
      - 093XXXXXXX
  - Xoá các outbound cũ không dùng nữa
    - Các outbound đã xoá:
     ![Ảnh 1](https://github.com/hiiamtu16/InternReport/blob/4fe85d5bfa785f7416069496641ead66463797b0/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/System/voip/C%C3%A1c%20outbound%20route%20%C4%91%C3%A3%20xo%C3%A1/1.png?raw=1)
     ![Ảnh 2](https://github.com/hiiamtu16/InternReport/blob/4fe85d5bfa785f7416069496641ead66463797b0/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/System/voip/C%C3%A1c%20outbound%20route%20%C4%91%C3%A3%20xo%C3%A1/2.png?raw=1)
     ![Ảnh 3](https://github.com/hiiamtu16/InternReport/blob/4fe85d5bfa785f7416069496641ead66463797b0/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/System/voip/C%C3%A1c%20outbound%20route%20%C4%91%C3%A3%20xo%C3%A1/3.png?raw=1)
     ![Ảnh 4](https://github.com/hiiamtu16/InternReport/blob/4fe85d5bfa785f7416069496641ead66463797b0/Picture%20/B%C3%A1o%20c%C3%A1o%20c%C3%B4ng%20vi%E1%BB%87c%20%C4%91%C3%A3%20ho%C3%A0n%20th%C3%A0nh/System/voip/C%C3%A1c%20outbound%20route%20%C4%91%C3%A3%20xo%C3%A1/4.png?raw=1)
    - Tắt `Route Selections` trong `Class of Service`
      - Tắt các outbound cần xoá như trên
      - Bật các outbound đã tạo mới 
