1. IP
  a. Định nghĩa
    IP (Internet Protocol) là giao thức Internet, một tập hợp các quy tắc cho phép các thiết bị trên mạng có thể nhận diện, định vị và giao tiếp với nhau. Mỗi thiết bị kết nối Internet sẽ được gán một địa chỉ IP giống như số nhà trên đường, giúp dữ liệu biết được nơi cần gửi đến và nơi gửi đi.
  b. IPv4 và IPv6
    * IPv4:
      - 32 bit chia thành 4 nhóm, mỗi nhóm 8 bit biểu diễn số từ 0 - 255, ngăn cách nhau bởi dấu "."
      - IPv4 phân thành các lớp:
        + Lớp A: bao gồm các địa chỉ IP có oc-tet đầu tiên mang giá trị từ 1 – 126, dành riêng cho địa chỉ của các tổ chức lớn trên thế giới. Địa chỉ lớp A từ 1.0.0.1 – 126.0.0.0
        + Lớp B: bao gồm các địa chỉ IP có oc-tet đầu tiên mang giá trị từ 128 – 191, dành cho tổ chức hạng trung trên thế giới. Địa chỉ lớp B từ 128.1.0.0 – 191.254.0.0
        + Lớp C: bao gồm các địa chỉ IP có oc-tet đầu tiên mang giá trị từ 192 – 223, dành cho các tổ chức nhỏ, bao gồm cả các máy tính cá nhân. Địa chỉ lớp C từ 192.0.1.0 – 223.255.254.0
        + Lớp D: bao gồm các địa chỉ IP có oc-tet đầu tiên mang giá trị từ 224 – 239, có 4 bit đầu tiên là 1110, dành cho phát thông tin (multicast/broadcast). Địa chỉ lớp D từ 224.0.0.0 – 239.255.255.255
        + Lớp E: bao gồm các địa chỉ IP có oc-tet mang giá trị từ 240 – 255, có 4 bit đầu tiên là 1111. Địa chỉ lớp E từ 240.0.0.0 – 254.255.255.255
        + Loopback: Lớp Loopback có địa chỉ 127.x.x.x, được sử dụng riêng để kiểm tra vòng lặp quy hồi
        
    * IPv6:
      - 128 bit chia thành 8 nhóm, mỗi nhóm 16 bit tạo thành 4 kí tự Hex từ 0 - F, ngăn cách nhau bởi dấu ":"
      - Quy tắc rút gọn
        + Bỏ các số 0 ở đầu mỗi nhóm
        + Dùng “::” để thay thế chuỗi nhiều nhóm 0 liên tiếp
        + Lưu ý: Dấu “::” chỉ dùng một lần trong một địa chỉ.
  c. Lệnh liên quan IP trên CMD
    * Xem IP hiện tại của máy: ipconfig /all
    * Huỷ IP hiện tại trên DHCP: ipconfig /release
    * Lấy IP từ DHCP: ipconfig /renew
  d. Subnet Mask
    * Subnet mask là một giá trị dùng để chia mạng IP thành nhiều mạng con (subnet). Nó giúp xác định phần địa chỉ Network và địa chỉ Host
    * Subnet mask
3. Gateway
  
4. DNS

5. DHCP

6. Private & Public IP

7. NAT

8. Default Route

9. MAC
