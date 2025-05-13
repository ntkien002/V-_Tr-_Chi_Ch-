# https://hackerone.com/reports/1780399
---

### 💥 LỖ HỔNG: **Không giới hạn tốc độ gửi mã OTP**

* **Mục tiêu**: [https://play.mtn.co.za/authorise/](https://play.mtn.co.za/authorise/)
* **Ảnh hưởng**: SMS bombing → khiến nạn nhân bị spam hàng loạt mã OTP liên tục trong thời gian ngắn.

---

### 🚀 PoC Tái hiện (Dùng Burp Suite + Intruder)

**Bước 1:** Bật Intercept trong tab Proxy
**Bước 2:** Truy cập \[link target], điền form đăng nhập/số điện thoại → gửi
**Bước 3:** Trong Burp → Proxy → HTTP History
→ tìm request `POST /nim/otp`
→ Chuột phải → **Send to Intruder**

**Bước 4:** Trong tab Intruder:

* Chọn “Positions” → **Clear all** positions
* Qua “Payloads”:

  * Chọn payload type: **Null payloads**
  * Số lượng: 100 hoặc nhiều hơn tùy test

**Bước 5:** Bấm **Start Attack** → hệ thống gửi spam OTP không giới hạn

> Lưu ý: Không có kiểm soát **rate-limit**, **CAPTCHA**, hay **cooldown** → dẫn đến khả năng lạm dụng dễ dàng.

---

### 🧠 Góc khai thác và nâng cao:

* Kịch bản tấn công thực tế:
  🔸 DDoS nhẹ gây bất tiện người dùng
  🔸 Kết hợp kỹ thuật *SIM swapping* / *OTP hijacking* / *social engineering*
  🔸 Lợi dụng để chiếm đoạt tài khoản nếu OTP là bước xác thực duy nhất

