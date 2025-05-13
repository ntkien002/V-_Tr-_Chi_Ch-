tức là khi một ứng dụng web **cho phép người dùng can thiệp vào nội dung của một template (giao diện hoặc logic xử lý mẫu)** mà không lọc dữ liệu đầu vào đúng cách.

---

### 🧠 Tóm gọn định nghĩa:

> **Template Injection RCE** là kỹ thuật lợi dụng lỗ hổng *Template Injection* để **thực thi mã từ xa (Remote Code Execution)** trên server chạy ứng dụng.

---

### 🔥 Mô hình lỗ hổng:



3. Nếu attacker gửi `?name={{7*7}}`, server sẽ trả về `"Hello 49"` → **SSTI** (Server-Side Template Injection).

---


---

### 🕵️‍♂️ Cách phát hiện:

* Dò payload đơn giản:

  * `{{7*7}}`, `{{1337+1}}`
  * `#{7*7}` với Ruby (ERB)
* Quan sát phản hồi từ server → nếu trả về kết quả tính toán => có khả năng injection.

---

