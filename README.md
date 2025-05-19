
**MỤC LỤC**

1. **Nhận dạng (THÔNG TIN)**

### 1.1. Tiến hành Khám phá và Trinh sát Công cụ Tìm kiếm để Rò rỉ Thông tin:
- Sử dụng Google Dorks để tìm các tệp hoặc thông tin nhạy cảm trên một trang web.
-  Ví dụ: Tìm các tệp PDF chứa thông tin nhạy cảm trên một domain cụ thể: site:example.com filetype:pdf "confidential".
### 1.2. Máy chủ Web vân tay:
- Xác định loại máy chủ web (như Apache, Nginx) bằng cách kiểm tra tiêu đề HTTP.
- Ví dụ: Sử dụng lệnh curl -I http://example.com để xem tiêu đề Server: Apache/2.4.41.
### 1.3. Xem lại các tệp siêu dữ liệu của máy chủ web để tìm rò rỉ thông tin:
- Kiểm tra các tệp như robots.txt hoặc sitemap.xml để tìm thông tin về cấu trúc trang web.
- Ví dụ: Truy cập http://example.com/robots.txt và thấy dòng Disallow: /admin, tiết lộ thư mục quản trị.
### 1.4. Liệt kê các ứng dụng trên máy chủ web:
- Dùng công cụ như WhatWeb hoặc Wappalyzer để liệt kê các ứng dụng hoặc CMS (như WordPress, Joomla) chạy trên máy chủ.
- Ví dụ: Chạy whatweb example.com và nhận kết quả: WordPress[4.9.8], PHP[7.2].
### 1.5. Xem lại các bình luận và siêu dữ liệu trang web để tìm rò rỉ thông tin:
- Kiểm tra mã nguồn HTML để tìm các bình luận hoặc siêu dữ liệu (metadata) chứa thông tin nhạy cảm.
- Ví dụ: Trong mã nguồn HTML của http://example.com, tìm thấy bình luận <!-- API Key: 12345xyz -->.
### 1.6. Xác định điểm nhập ứng dụng:
- Tìm các điểm nhập như biểu mẫu đăng nhập, API endpoint, hoặc trang quản trị.
- Ví dụ: Truy cập http://example.com/login và phát hiện một biểu mẫu đăng nhập không được bảo vệ.
### 1.7. Bản đồ các đường dẫn thực hiện thông qua ứng dụng:
- Sử dụng công cụ như Burp Suite để thu thập và lập bản đồ các URL mà ứng dụng sử dụng.
- Ví dụ: Sau khi thu thập, phát hiện các URL như http://example.com/api/v1/users và http://example.com/admin.
### 1.8. Khung ứng dụng web vân tay:
- Xác định khung công tác (framework) như Django, Laravel thông qua các dấu hiệu như cookie hoặc cấu trúc URL.
- Ví dụ: Trang web sử dụng cookie sessionid cho thấy khả năng dùng Django: curl http://example.com | grep sessionid.
### 1.9. Ứng dụng Web vân tay:
- Xác định ứng dụng cụ thể (như phpMyAdmin, Joomla) bằng cách kiểm tra các tệp hoặc thư mục đặc trưng.
- Ví dụ: Truy cập http://example.com/phpmyadmin và xác nhận phiên bản phpMyAdmin đang chạy.
### 1.10. Kiến trúc ứng dụng bản đồ:
- Vẽ sơ đồ cách ứng dụng tương tác, bao gồm máy chủ, cơ sở dữ liệu, và API.
- Ví dụ: Sử dụng công cụ như OWASP ZAP để lập bản đồ, thấy ứng dụng gọi API tại http://api.example.com và kết nối với cơ sở dữ liệu MySQL.




2. **Kiểm tra quản lý cấu hình và triển khai (CẤU HÌNH)**
   2.1.: Cấu hình mạng/cơ sở hạ tầng thử nghiệm
   2.2.: Cấu hình nền tảng ứng dụng thử nghiệm
   2.3.: Kiểm tra phần mở rộng tệp xử lý thông tin nhạy cảm
   2.4.: Sao lưu và các tập tin không có tham chiếu cho thông tin nhạy cảm
   2.5.: Liệt kê các giao diện quản trị cơ sở hạ tầng và ứng dụng
   2.6.: Kiểm tra phương pháp HTTP
   2.7.: Kiểm tra bảo mật truyền tải HTTP nghiêm ngặt
   2.8.: Kiểm tra chính sách miền chéo RIA
   2.9.: Kiểm tra quyền tập tin
   2.10.: Kiểm tra việc tiếp quản tên miền phụ

3. **Kiểm tra quản lý danh tính (IDENT)**
   3.1.: Định nghĩa vai trò kiểm tra
   3.2.: Quy trình đăng ký người dùng thử nghiệm
   3.3.: Quy trình cung cấp tài khoản thử nghiệm
   3.4.: Kiểm tra để đếm tài khoản và đoán được tài khoản người dùng
   3.5.: Kiểm tra chính sách tên người dùng yếu hoặc không được thực thi
   3.6.: Kiểm tra quyền của tài khoản khách/đào tạo
   3.7.: Quy trình tạm dừng/tiếp tục tài khoản thử nghiệm

4. **Kiểm tra xác thực (XÁC THỰC)**
   4.1.: Kiểm tra thông tin xác thực được truyền qua kênh được mã hóa
   4.2.: Kiểm tra thông tin xác thực mặc định
   4.3.: Kiểm tra cơ chế khóa yếu
   4.4.: Kiểm tra để bỏ qua lược đồ xác thực
   4.5.: Kiểm tra chức năng ghi nhớ mật khẩu
   4.6.: Kiểm tra điểm yếu của bộ nhớ đệm trình duyệt
   4.7.: Kiểm tra chính sách mật khẩu yếu
   4.8.: Kiểm tra câu hỏi/câu trả lời bảo mật yếu
   4.9.: Kiểm tra chức năng thay đổi hoặc đặt lại mật khẩu yếu
   4.10.: Kiểm tra xác thực yếu hơn trong kênh thay thế

5. **Kiểm tra ủy quyền (AUTHZ)**
   5.1.: Kiểm tra duyệt thư mục/bao gồm tệp
   5.2.: Kiểm tra để bỏ qua lược đồ ủy quyền
   5.3.: Kiểm tra để tăng đặc quyền
   5.4.: Kiểm tra tham chiếu đối tượng trực tiếp không an toàn

6. **Kiểm tra quản lý phiên (PHIÊN)**
   6.1.: Kiểm tra để bỏ qua lược đồ quản lý phiên
   6.2.: Kiểm tra các thuộc tính Cookie
   6.3.: Kiểm tra sự cố định của phiên
   6.4.: Kiểm tra các biến phiên được hiển thị
   6.5.: Kiểm tra yêu cầu giả mạo trang web chéo
   6.6.: Kiểm tra chức năng đăng xuất
   6.7.: Thời gian chờ phiên kiểm tra
   6.8.: Kiểm tra cho phiên giải đố
   6.9.: Kiểm tra phiên Hijacking

7. **Kiểm tra xác thực dữ liệu (INPVAL)**
   7.1.: Kiểm tra cho Reflected Cross Site Scripting
   7.2.: Kiểm tra cho Stored Cross Site Scripting
   7.3.: Kiểm tra HTTP Verb Tampering
   7.4.: Kiểm tra ô nhiễm tham số HTTP
   7.5.: Kiểm tra SQL Injection \ Oracle, MySQL, SQL Server, PostgreSQL, MS Access, NoSQL, ORM, phía khách
   7.6.: Kiểm tra tiêm LDAP
   7.7.: Kiểm tra cho XML Injection
   7.8.: Kiểm tra tiêm SSI
   7.9.: Kiểm tra XPath Injection
   7.10.: Tiêm IMAP/SMTP
   7.11.: Kiểm tra tiêm mã (bao gồm LFI/RFI)
   7.12.: Kiểm tra lệnh tiêm
   7.13.: Kiểm tra cho việc tiêm chuỗi định dạng
   7.14.: Kiểm tra các lỗ hổng đã được ủ
   7.15.: Kiểm tra HTTP Splitting/Smuggling
   7.16.: Kiểm tra các yêu cầu đến HTTP
   7.17.: Kiểm tra Host Header Injection
   7.18.: Kiểm tra việc tiêm mẫu phía máy chủ
   7.19.: Kiểm tra yêu cầu giả mạo phía máy chủ

8. **Xử lý lỗi (ERR)**
   8.1.: Kiểm tra xử lý lỗi không đúng cách
   8.2.: Phân tích dấu vết ngăn xếp

9. **Mật mã học (CRYPST)**
   9.1.: Kiểm tra mã hóa SSL/TLS yếu, bảo vệ lớp truyền tải không đủ
   9.2.: Kiểm tra Padding Oracle
   9.3.: Kiểm tra thông tin nhạy cảm được gửi qua các kênh không được mã hóa

10. **Kiểm tra logic kinh doanh (BUSLOGIC)**
    10.1.: Kiểm tra xác thực dữ liệu logic kinh doanh
    10.2.: Kiểm tra khả năng rèn yêu cầu
    10.3.: Kiểm tra tính toàn vẹn của bài kiểm tra
    10.4.: Kiểm tra thời gian xử lý
    10.5.: Kiểm tra số lần một hàm có thể được sử dụng (Giới hạn)
    10.6.: Kiểm tra việc tránh né luồng công việc
    10.7.: Kiểm tra phòng thủ chống lại việc sử dụng sai ứng dụng
    10.8.: Kiểm tra Tải lên các loại tệp không mong muốn
    10.9.: Kiểm tra Tải lên các tập tin độc hại

11. **Kiểm tra phía khách hàng (KHÁCH HÀNG)**
    11.1.: Kiểm tra cho DOM dựa trên Cross Site Scripting
    11.2.: Kiểm tra thực thi JavaScript
    11.3.: Kiểm tra tiêm HTML
    11.4.: Kiểm tra chuyển hướng URL phía máy khách
    11.5.: Kiểm tra CSS Injection
    11.6.: Kiểm tra thao tác tài nguyên phía khách hàng
    11.7.: Kiểm tra Chia sẻ tài nguyên nguồn gốc chéo
    11.8.: Kiểm tra cho nhấp nháy chéo
    11.9.: Kiểm tra Clickjacking
    11.10.: Kiểm tra WebSockets
    11.11.: Kiểm tra tin nhắn web
    11.12.: Kiểm tra lưu trữ cục bộ
    11.13.: Kiểm tra để đưa tập lệnh CrossSite vào

—

