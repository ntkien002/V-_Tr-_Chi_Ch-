# https://hackerone.com/reports/2586433)

---

### 🚨 **Tình huống**

Hệ thống xử lý liên kết mời (invitation link) của `satismeter.com` **không ràng buộc chặt email được mời với liên kết**, dẫn tới việc **bất kỳ ai** cũng có thể đăng ký tài khoản và tham gia tổ chức bằng email **không được mời** => **Bypass xác minh email**.

---

### 🔥 **Chi tiết khai thác**

#### 💥 Lỗ hổng: *Email mismatch trong quá trình đăng ký tài khoản từ invitation link*

* Invitation link không được **ràng buộc chặt chẽ với email được mời**.
* Chỉ cần có invitation URL, attacker có thể thay email bất kỳ trong payload và đăng ký thành công.

---

### 🧪 **Steps to Reproduce (PoC)**

1. **Victim** gửi invitation đến email của attacker (`attacker@example.com`).
2. Attacker vào mail, nhấn link => **redirect đến trang thiết lập mật khẩu**.
3. Mở **Burp Suite**, bật **intercept**.
4. Nhập mật khẩu bất kỳ → Submit → bắt được request sau:

```http
POST /graphql HTTP/2
Content-Type: application/json

{
  "operationName": "SignUp",
  "variables": {
    "input": {
      "email": "example@gmailll.com",   <-- ⚠️ THAY bằng email KHÔNG được mời
      "link": null,
      "password": "wxxxxxxx",
      "source": "invitation"
    }
  },
  "query": "mutation SignUp($input: SignUpInput!) { auth { signUp(input: $input) __typename }}"
}
```

5. Forward request → Đăng ký thành công với email **không nằm trong danh sách được mời**.
6. Đăng nhập thành công với vai trò người dùng trong tổ chức.

---

### 🎯 **Tác động**

* **Bypass email verification**: Không cần xác thực quyền sở hữu email.
* **Account Takeover risk**: Nếu đoán đúng email có đặc quyền và có thể dùng lại invitation.
* **Privilege escalation**: Nếu invitation gán role admin/owner thì hậu quả rất nghiêm trọng.
* **Data leakage**: Attacker có quyền trong workspace, xem/modify dữ liệu nội bộ.

---

