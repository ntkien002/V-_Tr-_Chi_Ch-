# https://hackerone.com/reports/2778380
---

## 🧨 **Tổng quan lỗ hổng**

* **Loại lỗi**: LFI chưa xác thực.
* **Endpoint**: `/jolokia/exec/com.sun.management:type=DiagnosticCommand/compilerDirectivesAdd/`
* **Kỹ thuật khai thác**: Chèn biểu tượng `!` để vượt qua kiểm tra đường dẫn và duyệt file hệ thống.
* **Tác động**: Đọc được file nhạy cảm trên hệ thống **mà không cần xác thực**, dẫn đến khả năng rò rỉ dữ liệu, cấu hình, mã nguồn hoặc thậm chí **chuỗi khai thác tiếp theo như RCE**.

---

## 🧠 **Chi tiết kỹ thuật**

### 🔍 PoC mẫu:

* `https://target/jolokia/exec/com.sun.management:type=DiagnosticCommand/compilerDirectivesAdd/!/etc!/passwd`
* `https://target/jolokia/exec/com.sun.management:type=DiagnosticCommand/compilerDirectivesAdd/!/etc!/crontab`

Cú pháp `/!/etc!/passwd` cho thấy ký hiệu `!` được dùng để **thay thế cho ký tự `/`** – một kỹ thuật bypass filtering khá cổ điển nhưng vẫn hiệu quả nếu hệ thống parse không chặt.

---


---

