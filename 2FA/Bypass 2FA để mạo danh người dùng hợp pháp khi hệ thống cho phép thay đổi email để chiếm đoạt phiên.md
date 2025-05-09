# https://hackerone.com/reports/2885636
---

## 🧪 **Môi trường thiết lập PoC**

* Website đích: `https://www.drugs.com`
* Cần có:

  * 1 tài khoản tấn công (Người dùng A)
  * 1 email của nạn nhân (Người dùng B – chưa từng đăng ký tài khoản)
  * Trình duyệt hỗ trợ lưu cookie và session, hoặc sử dụng **Burp Suite** để theo dõi HTTP requests/responses

---

## 🔓 **PoC: Bỏ qua 2FA và mạo danh người dùng**

### 📍 **Bước 1 – Tạo tài khoản và xác thực OTP**

* Truy cập: `https://www.drugs.com/account/register/`
* Tạo tài khoản mới với **email của kẻ tấn công** (Người dùng A)
* Hoàn tất xác minh OTP (mã gửi qua email)
* Chọn tuỳ chọn **“Tin cậy thiết bị trong 1 tháng”** ⇒ phiên login được đánh dấu là trusted (bỏ qua 2FA)

✅ *Kết quả: Tài khoản A đã login và có phiên trusted (miễn 2FA trong 30 ngày)*

---

### 📍 **Bước 2 – Mạo danh bằng cách thay đổi email**

* Truy cập: `https://www.drugs.com/account/details/`
* **Thay đổi email** sang địa chỉ của nạn nhân (Người dùng B) – người chưa từng đăng ký

✅ *Kết quả: Phiên trusted hiện tại (cookie/session) vẫn còn hiệu lực và giờ được gắn với email của Người dùng B*

⛔ **Không có 2FA hay OTP gửi lại** → bypass full xác thực

---

### 📍 **Bước 3 – Xác minh bypass và duy trì quyền truy cập**

* Đăng xuất → sau đó **đăng nhập lại bằng email của nạn nhân (Người dùng B)**
* **Không cần OTP**, login thành công luôn do đã đánh dấu "trusted device"

→ Đây là lúc kẻ tấn công **chiếm quyền tài khoản chưa từng tồn tại nhưng giờ đã "bị chiếm"**

---

## 🔁 **Lặp lại & Duy trì session vĩnh viễn**

* Vòng lặp duy trì quyền truy cập:

  1. Đổi lại email thành email của kẻ tấn công
  2. Xác minh lại bằng OTP, chọn lại "Tin cậy thiết bị trong 1 tháng"
  3. Đổi lại email về địa chỉ của nạn nhân
  4. Lặp lại quá trình để **reset trusted session mà không mất quyền truy cập**

> 🧠 **Điểm khai thác cốt lõi**: Việc thay đổi email **không hủy session hiện tại**, và hệ thống **không kiểm tra lại quyền sở hữu email mới** → hoàn toàn bypass 2FA theo thiết kế logic.

---

## 🎭 **Góc nhìn từ phía nạn nhân**

* Khi nạn nhân truy cập vào `https://www.drugs.com/account/register/` và nhập email của mình:

  * Sẽ nhận được thông báo "Email đã tồn tại"
  * Tuy nhiên, **họ chưa bao giờ đăng ký tài khoản**
  * Không có cách nào lấy lại quyền trừ khi **thử reset mật khẩu** (khi đó tấn công mới mất quyền)

hay đổi email tài khoản
* Cơ chế ghi nhớ thiết bị / trusted session
* Không tự động hủy session khi thay đổi thông tin nhạy cảm như email

---

Bu có cần bu mô phỏng lại từng request bằng Burp Suite hay viết payload chi tiết trong JSON/HTTP format không?
