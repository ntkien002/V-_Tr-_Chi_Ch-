# https://hackerone.com/reports/2762462
---

## 🧪 Môi trường thiết lập PoC

* **URL đích**: `https://shop.mtn.ng`
* **Tài khoản attacker**: bất kỳ email hợp lệ (ví dụ `attacker@example.com`)
* **Số điện thoại victim**: bất kỳ số Nigeria (MSISDN) bạn muốn chiếm → không cần sở hữu thực
* **Công cụ**:

  * Trình duyệt (Chrome/Firefox) thiết lập proxy với **Burp Suite** hoặc **Caido** để intercept và chỉnh sửa request/response.
  * Email tester (chỉ để đăng ký tài khoản, không dùng OTP của victim).

---

## 🔓 PoC Bypass OTP và ATO

### ▶️ Bước 1 – Tạo tài khoản và thêm số điện thoại

1. **Đăng ký tài khoản**

   * Truy cập trang “Login/Signup” → “Create an Account”.
   * Điền thông tin cơ bản (First Name, Last Name, Email, Password).
   * Submit form, nhận email xác nhận (nếu có), hoàn tất registration.

2. **Chọn “Manage Account” → “Edit”**

   * Tìm mục “Mobile Number” → nhập một số Nigeria bất kỳ (ví dụ `+2348032343997`).
   * Click “Save” để hệ thống gửi **OTP** về số đó.

---

### ▶️ Bước 2 – Intercept và Manipulate OTP Verification

1. **Gửi yêu cầu verify OTP**

   * Khi màn hình yêu cầu nhập “OTP”, điền **OTP giả** (ví dụ `123456`).
   * Quan sát HTTP request gửi đến `/mtn_otp/index/verification/`:

     ```
     POST /mtn_otp/index/verification/ HTTP/2
     Host: shop.mtn.ng
     Content-Type: application/x-www-form-urlencoded

     ajax=1&action=verifyotp&msisdn=2348032343997&otp=123456
     ```
2. **Chỉnh sửa response từ server**

   * Server gốc trả về:

     ```json
     {"status":400,"message":"Invalid OTP","msisdn":"2348032343997","success":false}
     ```
   * Trong Burp/Caido, sửa thành:

     ```json
     {"status":200,"msisdn":"2348032343997","success":true}
     ```
   * Forward response đã chỉnh → client tin rằng OTP hợp lệ dù không có mã đúng.

---

### ▶️ Bước 3 – Xác nhận ATO và IDOR

1. **Kiểm tra quyền truy cập**

   * Reload trang “Manage Account” hoặc chuyển qua bất kỳ chức năng bảo mật (ví dụ xem lịch sử giao dịch).
   * Nếu thành công, bạn đã **chiếm quyền số điện thoại của victim** trên account attacker.
2. **Thử IDOR**

   * Sau khi đã add số, truy vấn các API lấy lịch sử giao dịch của số đó (endpoints nội bộ).
   * Bạn sẽ thấy thông tin nhạy cảm, chứng tỏ lỗ hổng IDOR kèm theo.

---
