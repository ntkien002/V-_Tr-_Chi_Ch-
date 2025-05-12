# https://hackerone.com/reports/1556950
---

## 💥 Lỗ Hổng

### 🔓 Vấn đề:

Người dùng **đã xác thực** có thể xem **dữ liệu của người dùng khác** thông qua việc **manipulate URL** với các endpoint:

* `/SelfService/Home/dynamicdata/section/...`
* Có thể thay đổi tham số trong URL (ví dụ: `/61/124948002`) để **xem thông tin lính khác**:

  * Số SSN (4 số cuối)
  * Địa chỉ nhà
  * MOS (Military Occupational Specialty)
  * Lịch sử trường lớp, khoá huấn luyện

### 📚 Tham chiếu:

* CWE-359: [Exposure of Private Personal Information](https://cwe.mitre.org/data/definitions/359.html)
* CWE-284: Improper Access Control
* CWE-200: Information Disclosure

---

## 🧪 PoC (Proof of Concept) kỹ thuật

```plaintext
1. Đăng nhập hệ thống: https://█████████/SelfService/home/selfservice
2. Bật Burp Suite, Intercept OFF
3. Truy cập trang “My █████ Data”
4. Trong tab HTTP History, tìm request `GET` chứa URL dạng:
   /SelfService/Home/dynamicdata/section/XXX/XXX%20TPU/61/124948002
5. Gửi request này vào Intruder
6. Trong tab Positions:
   - Chọn số ID `124948002` hoặc đoạn `/61/124948002`
   - Add biến payload cho phần này
7. Trong tab Payloads:
   - Payload Type: Numbers
   - From: 1 → To: 999999999 (hoặc một phạm vi nhỏ để test)
   - Step: 1
8. Trong tab Options:
   - Bật "Grep - Match" → `Primary MOS`
9. Chạy fuzz → thu được dữ liệu của nhiều cá nhân khác nhau
```

---

## 🎯 Tác Động

* Rò rỉ thông tin cực kỳ **nhạy cảm**:

  * SSN (4 số cuối)
  * Địa chỉ cá nhân
  * Thông tin nghề nghiệp quân sự
  * Lịch sử đào tạo và công tác
* Kẻ thù có thể **thu thập hồ sơ cá nhân của quân nhân** → **đe doạ tính mạng hoặc chiêu mộ làm gián điệp**

---

