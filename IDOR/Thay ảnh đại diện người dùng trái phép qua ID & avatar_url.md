# https://hackerone.com/reports/2962056
---

## 🪪 **IDOR – Thay ảnh đại diện người dùng trái phép**

### 🔎 **Mục tiêu:**

* `https://profile.autodesk.com`
* Endpoint thay ảnh đại diện user

### 🧠 **Mô tả:**

Ứng dụng **không xác thực quyền sở hữu tài nguyên**, cho phép attacker thay đổi ảnh đại diện của **người dùng bất kỳ** chỉ bằng cách thay đổi tham số `id` trong HTTP request.

→ **Lỗi IDOR (Insecure Direct Object Reference)** – phổ biến nhưng nghiêm trọng nếu không có check ownership hoặc token định danh.

---

### 💥 **PoC Khai thác (minh họa):**

```http
POST /api/user/avatar
Host: profile.autodesk.com
Authorization: Bearer <token của attacker>

{
  "id": "victim_user_id",
  "avatar_url": "https://attacker.com/malicious_avatar.jpg"
}
```

→ Ảnh đại diện người dùng **nạn nhân** bị thay đổi ngay lập tức.

---
