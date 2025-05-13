# https://hackerone.com/reports/2627062

---
## 📍 ** Bypass Rate Limiting + Geo-IP Restriction**

### 🔥 Kỹ thuật:

1. Gửi **login brute force** hoặc **OTP verify** đến `https://passport.acronis.work/`

2. Server rate-limit (HTTP 429) sau một số request ⇒ Giới hạn này **dựa vào IP**

3. Bu spoof IP bằng cách thêm `X-Forwarded-For: <random-IP>` ⇒ Mỗi request là IP mới ⇒ bypass limit

4. Còn có trường hợp site kiểm tra vị trí địa lý IP (chỉ người dùng ở Bulgaria được login) ⇒ bu spoof `X-Forwarded-For: 109.104.192.0` (IP từ Bulgaria) ⇒ bypass restriction.

### 🚨 Impact:

* Cho phép brute-force OTP
* Cho phép vượt geo-restriction để login tài khoản nội bộ (ví dụ email nhân viên lấy từ OSINT)
* **Access Control Misconfiguration** nghiêm trọng hơn là chỉ brute-force

### ✅ POC:

```http
POST /login HTTP/1.1
Host: passport.acronis.work
X-Forwarded-For: 109.104.192.0
Content-Type: application/json

{
  "email": "ab@acronis.com",
  "password": "WrongPassword123"
}
```

→ Server cho phép thử vì nghĩ bu đến từ Bulgaria.

### 🧠 Phân tích kỹ thuật:

* Rate limit sai chỗ: chỉ dựa vào IP client hoặc `X-Forwarded-For` mà không verify IP thực từ reverse proxy.
* Hạn chế quốc gia nhưng lại xác định quốc gia **qua IP trong header** ⇒ dễ bị giả mạo.

---

## 🧬 Tổng hợp chiến thuật khai thác:

* **Bypass IP-based security bằng header `X-Forwarded-For`** là kỹ thuật cổ điển nhưng vẫn bị bỏ sót nhiều.
* Nếu hệ thống không có reverse proxy rõ ràng xác thực `X-Forwarded-For`, thì mọi kiểm tra IP đều có thể bị giả mạo.
* Kết hợp brute-force + bypass geo-IP + bypass rate-limit tạo thành chuỗi tấn công chiếm đoạt account cao cấp, đặc biệt nếu có OTP endpoint dở hơi (cho phép thử liên tục).

---

