
**MỤC LỤC**

# 1. **Nhận dạng (THÔNG TIN)**

**Khám phá và Trinh sát (Reconnaissance & Discovery)** trong pentest web:

| **STT**  | **Hoạt động**                     | **Chi tiết**                                           | **Ví dụ**                                                 |
| -------- | --------------------------------- | ------------------------------------------------------ | --------------------------------------------------------- |
| **1.1**  | Khai thác công cụ tìm kiếm        | Sử dụng Google Dorks để tìm thông tin nhạy cảm         | `site:example.com filetype:pdf "confidential"`            |
| **1.2**  | Vân tay máy chủ web               | Kiểm tra loại máy chủ qua tiêu đề HTTP                 | `curl -I http://example.com` → `Server: Apache/2.4.41`    |
| **1.3**  | Phân tích tệp siêu dữ liệu        | Xem `robots.txt`, `sitemap.xml` để thu thập thông tin  | `http://example.com/robots.txt` → `Disallow: /admin`      |
| **1.4**  | Liệt kê ứng dụng chạy trên server | Dùng WhatWeb hoặc Wappalyzer để xác định CMS           | `whatweb example.com` → `WordPress[4.9.8], PHP[7.2]`      |
| **1.5**  | Tìm rò rỉ trong mã HTML           | Phân tích comment và metadata                          | `<!-- API Key: 12345xyz -->` trong mã nguồn               |
| **1.6**  | Xác định điểm nhập ứng dụng       | Tìm login forms, admin pages, API endpoint             | `http://example.com/login` có form đăng nhập không bảo vệ |
| **1.7**  | Lập bản đồ các endpoint           | Dùng Burp Suite để thu thập URL                        | Phát hiện: `/api/v1/users`, `/admin`                      |
| **1.8**  | Vân tay framework                 | Nhận dạng Django, Laravel,... qua cookies/URL          | `sessionid` → Có thể là Django                            |
| **1.9**  | Vân tay ứng dụng web              | Xác định ứng dụng như phpMyAdmin qua thư mục đặc trưng | `http://example.com/phpmyadmin`                           |
| **1.10** | Bản đồ kiến trúc ứng dụng         | Vẽ sơ đồ liên kết server, API, DB                      | Dùng Burp suite → `api.example.com` → MySQL backend        |




---

2. **Kiểm tra quản lý cấu hình và triển khai (CẤU HÌNH)**
** Chuẩn bị Môi trường Thử nghiệm (Testing Environment Setup)**, cấu trúc tương tự phần 1:

| **STT**  | **Hoạt động**                                      | **Chi tiết**                                                                                         | **Ví dụ**                                                                                                       |
| -------- | -------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **2.1**  | Cấu hình mạng / cơ sở hạ tầng thử nghiệm           | Thiết lập mạng riêng biệt (VLAN, VPN) và quy tắc firewall để tránh ảnh hưởng lên môi trường sản xuất | VLAN 192.168.100.0/24, firewall chỉ cho phép HTTP(S) từ IP của pentester                                        |
| **2.2**  | Cấu hình nền tảng ứng dụng thử nghiệm              | Triển khai phiên bản staging/dev của ứng dụng với cùng stack công nghệ và cấu hình tương tự sản xuất | Dùng Docker Compose để khởi tạo webapp (Nginx+PHP) và database (MySQL)                                          |
| **2.3**  | Kiểm tra phần mở rộng tệp xử lý thông tin nhạy cảm | Xác định xem server có cho phép upload hoặc phục vụ các extension chứa data nhạy cảm hay không       | Phát hiện file .bak, .old, .sql nằm trong thư mục public\_html                                                  |
| **2.4**  | Kiểm tra file sao lưu và tập tin vô tham chiếu     | Tìm kiếm các bản backup, các file cũ không còn link nhưng vẫn tồn tại trên server                    | `config.php.bak`, `database.sql.gz` rơi vào thư mục `/backup/` nhưng có thể truy cập qua web                    |
| **2.5**  | Liệt kê giao diện quản trị                         | Xác định các panel quản trị hạ tầng (router, switch) và ứng dụng (CMS, API admin)                    | Phát hiện phpMyAdmin ở `http://test.example.com/phpmyadmin` và trang /admin của WordPress                       |
| **2.6**  | Kiểm tra phương thức HTTP                          | Kiểm tra các HTTP methods được server chấp nhận (PUT, DELETE, TRACE…)                                | `curl -X OPTIONS http://test.example.com -i` → cho phép `DELETE`                                                |
| **2.7**  | Kiểm tra bảo mật truyền tải HTTP nghiêm ngặt       | Xác minh HSTS, TLS version, cipher suite, redirect HTTP→HTTPS                                        | `curl -I https://test.example.com` → có header `Strict-Transport-Security: max-age=63072000; includeSubDomains` |
| **2.8**  | Kiểm tra chính sách cross‑domain (CORS / RIA)      | Đánh giá header `Access-Control-Allow-*`, xác định domain nào được phép cross‑origin                 | `curl -I http://api.test.example.com` → `Access-Control-Allow-Origin: *`                                        |
| **2.9**  | Kiểm tra quyền file hệ thống                       | Xem permission và ownership của các file/config nhạy cảm                                             | `ls -l /var/www/html/config.php` → `-rw-r--r--` (644) nên để 600                                                |
| **2.10** | Kiểm tra tiếp quản subdomain                       | Phát hiện DNS records trỏ đến dịch vụ cloud không sử dụng hoặc “dang dở”                             | `sub.test.example.com` CNAME → `xyz.s3.amazonaws.com` nhưng bucket S3 đã bị xóa → có thể takeover               |




---

3. **Kiểm tra quản lý danh tính (IDENT)**
 Quản lý Tài khoản Thử nghiệm (Test Account Management)**:

| **STT** | **Hoạt động**                                    | **Chi tiết**                                                                                         | **Ví dụ**                                                                                             |
| ------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **3.1** | Định nghĩa vai trò kiểm tra                      | Xác định rõ các quyền và phạm vi của từng loại tài khoản (Admin, User, Guest, Tester…)               | Phân quyền: Tester chỉ có quyền đọc; User có quyền đọc/ghi; Admin có quyền toàn bộ                    |
| **3.2** | Quy trình đăng ký người dùng thử nghiệm          | Thiết lập form hoặc API để tạo tài khoản thử nghiệm, đảm bảo có xác thực captcha hoặc email verify   | Gửi email kích hoạt đến [tester@example.com](mailto:tester@example.com) trước khi kích hoạt tài khoản |
| **3.3** | Quy trình cung cấp tài khoản thử nghiệm          | Cung cấp thông tin đăng nhập (username/password) cho tester qua kênh an toàn (e.g. vault, PGP email) | Lưu credentials trên HashiCorp Vault, chỉ tester mới có quyền truy cập                                |
| **3.4** | Kiểm tra để đếm tài khoản và đoán được tài khoản | Tìm lỗ hổng enumeration qua API hoặc response timing để phát hiện số lượng và pattern tài khoản      | Gọi `GET /api/users?page=1` và đoán username theo index (user1, user2…)                               |
| **3.5** | Kiểm tra chính sách tên người dùng               | Đánh giá độ mạnh, độ dài tối thiểu và ký tự cho phép của username; thử bypass rule                   | Tạo username “admin ” (có khoảng trắng) hoặc “<script>” qua form đăng ký để kiểm tra validate         |
| **3.6** | Kiểm tra quyền của tài khoản Guest/Đào tạo       | Xác minh tài khoản hạn chế không thể truy cập vùng hoặc API không thuộc quyền                        | Dùng Guest account thử truy cập `/admin/dashboard` → phải trả về 403                                  |
| **3.7** | Quy trình tạm dừng/tiếp tục tài khoản thử nghiệm | Thực hiện disable/enable tài khoản khi không cần dùng, đảm bảo token/ session bị thu hồi             | Chạy API `POST /users/{id}/disable` → user bị logout, không thể login lại cho đến khi enable lại      |




---

4. **Kiểm tra xác thực (XÁC THỰC)**
 **Kiểm thử Xác thực (Authentication Testing)**:

| **STT**  | **Hoạt động**                                            | **Chi tiết**                                                                                                      | **Ví dụ**                                                                                       |
| -------- | -------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **4.1**  | Kiểm tra thông tin xác thực qua kênh được mã hóa         | Đảm bảo credentials (username/password, tokens) luôn đi qua HTTPS/TLS, không để lộ qua HTTP hoặc kênh không kỳ mã | Quan sát `POST /login` phải là `https://`; nếu `http://` → MITM sniff credentials               |
| **4.2**  | Kiểm tra thông tin xác thực mặc định                     | Xem có tài khoản mặc định (admin/admin, root/password) chưa được đổi mật khẩu sau deploy                          | Đăng nhập với `admin:admin` hoặc `root:password` thành công                                     |
| **4.3**  | Kiểm tra cơ chế khóa tài khoản yếu                       | Test số lần đăng nhập sai trước khi khóa, thời gian khóa, khả năng reset tự động                                  | Thử 10 lần sai mật khẩu vẫn không bị khóa, hoặc khóa nhưng tự mở lại sau 1 phút                 |
| **4.4**  | Kiểm tra bypass lược đồ xác thực                         | Xác minh không thể vượt qua authentication bằng kỹ thuật như HTTP verb tampering, URL parameter tampering         | Gửi `GET /admin?auth=true` thay vì POST; nếu vẫn truy cập được → bypass                         |
| **4.5**  | Kiểm tra chức năng “ghi nhớ mật khẩu” (Remember Me)      | Đánh giá độ an toàn của cookie “remember\_me”: HttpOnly, Secure, expiration, khả năng forge                       | Cookie `remember_me=abcd1234` không có flag Secure/HttpOnly và thời gian quá dài                |
| **4.6**  | Kiểm tra điểm yếu bộ nhớ đệm trình duyệt (Browser Cache) | Đảm bảo không cache trang chứa sensitive data sau logout                                                          | Logout rồi bấm Back trên trình duyệt vẫn xem được `/dashboard` → cache control header chưa đúng |
| **4.7**  | Kiểm tra chính sách mật khẩu yếu                         | Đánh giá độ dài tối thiểu, complexity, blacklist, kiểm tra cho phép mật khẩu yếu như “123456”                     | Cho phép đặt password “password” hoặc “123456” mà không báo lỗi                                 |
| **4.8**  | Kiểm tra câu hỏi bảo mật yếu                             | Xem câu hỏi/đáp bảo mật dễ đoán hoặc reuse, khả năng bruteforce                                                   | Câu hỏi “Tên thú cưng đầu tiên” quá phổ biến, thử “Fluffy” thành công reset mật khẩu            |
| **4.9**  | Kiểm tra chức năng thay đổi/đặt lại mật khẩu             | Đánh giá quy trình reset: token duy nhất, hết hạn, gửi email xác thực, không leak user ID                         | Reset link chứa user\_id=1001 dễ đoán → thử đổi thành 1002 để reset mật khẩu của user khác      |
| **4.10** | Kiểm tra xác thực kênh thay thế (out-of-band) yếu        | Đánh giá phương pháp xác thực qua SMS/email/OTP, khả năng replay, man-in-the-middle                               | Nhận OTP rồi resend request cũ hoặc reuse OTP sau khi đã dùng lần đầu → vẫn được chấp nhận      |





---

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

