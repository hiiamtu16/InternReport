# Khái niệm Git và GitHub

## Git
Git là một hệ thống quản lý phiên bản phân tán DVCS giúp theo dõi các thay đổi trong mã nguồn khi phát triển phần mềm. Git được thiết kế để hoạt động hiệu quả với cả dự án nhỏ và lớn, hỗ trợ làm việc nhóm và giúp lập trình viên quản lý lịch sử thay đổi của tệp tin.

**Nơi làm việc**  
Hoạt động trên máy tính cá nhân của bạn và được sử dụng để làm việc với các kho lưu trữ repository cục bộ.

**Mục đích**  
Giúp bạn làm việc hiệu quả với mã nguồn, ngay cả khi không có kết nối internet hoặc khi làm việc một mình.

---

## GitHub
GitHub là nền tảng lưu trữ mã nguồn dựa trên Git, cung cấp dịch vụ quản lý mã nguồn từ xa remote repository, hỗ trợ làm việc nhóm, chia sẻ mã nguồn và đóng góp cộng đồng. Đây cũng là mạng xã hội dành cho lập trình viên.

**Nơi làm việc**  
Hoạt động trực tuyến, trên các máy chủ từ xa.

**Mục đích**  
Cho phép nhiều người cùng làm việc trên dự án, xem xét mã của nhau, quản lý nhiệm vụ và phát triển phần mềm hiệu quả.

**Quan hệ với Git**  
GitHub là dịch vụ được xây dựng dựa trên Git, cung cấp nơi lưu trữ kho Git cùng các tính năng cộng đồng.

---

# Sự khác biệt giữa Git và GitHub

| Tính năng | Git | GitHub |
|----------|------|---------|
| Loại hình | Phần mềm | Dịch vụ |
| Giao diện | Dòng lệnh | Giao diện người dùng đồ họa GUI |
| Lưu trữ | Cài đặt cục bộ | Lưu trữ trên web |
| Quản lý | Cộng đồng Linux bảo trì | Microsoft quản lý |
| Tập trung | Kiểm soát phiên bản và chia sẻ mã | Lưu trữ mã nguồn tập trung |
| Mục đích | Quản lý lịch sử mã nguồn | Lưu trữ và quản lý repository Git |
| Phát hành | 2005 | 2008 |
| Quản lý người dùng | Không có | Có tích hợp |
| Giấy phép | Mã nguồn mở | Miễn phí và trả phí |
| Công cụ ngoài | Cần cấu hình tối thiểu | Có marketplace tích hợp |
| Giao diện Desktop | Git GUI | GitHub Desktop |
| Đối thủ cạnh tranh | CVS, Azure DevOps Server, Subversion, Mercurial | GitLab, Bitbucket, AWS CodeCommit |

---

# Tác dụng của GitHub

## 1. Tập trung mã nguồn và tài liệu
GitHub lưu trữ tất cả mã nguồn và tài liệu trong các repository, giúp mọi người dễ dàng truy cập và đóng góp.  
Mỗi repository thường có hướng dẫn rõ ràng để hỗ trợ người tham gia.

## 2. Quản lý xung đột mã
GitHub hiển thị chi tiết các thay đổi khi nhiều lập trình viên chỉnh sửa cùng phần mã.  
Nhờ đó phát hiện xung đột sớm và tránh ảnh hưởng đến toàn dự án.

## 3. Theo dõi và khôi phục phiên bản
GitHub sử dụng Git để lưu lại mọi thay đổi qua từng commit.  
Bạn có thể khôi phục phiên bản cũ, xem ai thay đổi gì và khi nào.

---

# Các khái niệm Git cần nắm

### git  
Prefix cho các lệnh Git khi dùng dòng lệnh CLI.

### branch  
Nhánh dùng để tách các phiên bản khác nhau để phát triển song song.

### commit  
Một điểm lưu trạng thái mã nguồn trên cây phát triển Work Tree.

### clone  
Sao chép repository từ server Bitbucket, GitHub, GitLab, Gitea, v.v. về máy để tiếp tục phát triển.

### fork  
Sao chép repository của người khác về tài khoản GitHub của mình, hoạt động như một repo riêng.

### repository  
Kho lưu trữ dữ liệu và mã nguồn của dự án.

### tag  
Đánh dấu một commit quan trọng để dễ quản lý khi có quá nhiều commit.

### remote  
Điều khiển và đồng bộ các nhánh từ repository trên Git server.

### diff  
So sánh sự khác biệt giữa hai phiên bản mã nguồn.

### .gitignore  
File chứa danh sách các tệp hoặc thư mục không muốn Git theo dõi hoặc push lên server.

