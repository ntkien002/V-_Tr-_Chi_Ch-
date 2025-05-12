# https://hackerone.com/reports/2387297
---

### 🔥 **Phân tích lỗ hổng**

**Dịch vụ bị ảnh hưởng**: `monitor.firefox.com` / `stage.firefoxmonitor.nonprod.cloudops.mozgcp.net`

**Lỗi logic chính**:

* Sau khi gửi email xác minh, **`verification_token` bị lộ trong response HTTP**, đáng lẽ token này phải chỉ được gửi vào email.
* Không có xác thực đầu cuối giữa token và session user, nên **ai cũng có thể dùng token đó để xác minh email bất kỳ**.

---

### 📌 **POC tấn công (Proof-of-Concept)**

1. Truy cập site `https://monitor.firefox.com`

2. Thêm email bất kỳ (email nạn nhân) → Gửi yêu cầu xác minh.

3. Trong **Burp Suite**, bật *Intercept Response* (hoặc Burp Repeater nếu đã log traffic).

4. Tìm `verification_token` trong JSON response.

5. Truy cập URL:

   ```
   https://stage.firefoxmonitor.nonprod.cloudops.mozgcp.net/api/v1/user/verify-email?token={verification_token}
   ```

6. **Email của nạn nhân được xác minh mà không cần truy cập inbox.**

---

### 🧨 **Impact thực tế**

* **Thêm email người khác vào tài khoản của attacker** → kiểm soát dịch vụ giám sát vi phạm của nạn nhân.
* **Nếu cấu hình "gửi cảnh báo tới email chính"**, attacker nhận alert mỗi khi có leak thông tin của nạn nhân.
* **Truy cập thông tin leak từ dashboard**: có thể chứa thông tin cá nhân như **số điện thoại**, địa chỉ, dịch vụ đã rò rỉ,...

---

