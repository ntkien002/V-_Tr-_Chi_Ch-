**Tóm tắt lỗi:**

Đây là lỗi **Authentication Bypass via Airplane Mode** trong Worldcoin iOS app - cho phép user bị block vì underage tự unlock account mà không cần contact support, bằng cách sử dụng airplane mode trick.

**Nguyên nhân:**  
App hiển thị different UI flows dựa trên network connectivity state. Khi offline (airplane mode), app hiển thị option để unlock bằng passport verification thay vì yêu cầu contact support.

---

## **Chi tiết kỹ thuật:**

### **Luồng bình thường (underage block):**
```
1. User nhập DOB dưới age limit (ví dụ: 2015)
2. App phát hiện underage → block account
3. Hiển thị message: "Contact support to unlock"
4. KHÔNG có option nào khác
```

### **Luồng bị bypass (airplane mode):**
```
1. User bị block vì underage
2. Bật airplane mode → offline
3. Mở app → app không thể check server status
4. App hiển thị fallback UI: "Verify with passport"
5. User có thể tự unlock mà không cần support
```

---

## **Các bước khai thác:**

### **Bước 1: Tạo underage account**
```swift
// Trong app, thay đổi DOB thành underage
let underageDOB = Date(2015, 1, 1)  // 10 tuổi
userProfile.dateOfBirth = underageDOB
app.saveProfile()
```

### **Bước 2: Trigger age verification block**
```
1. Close app hoàn toàn
2. Re-open app
3. App sync với server
4. Server phát hiện underage → block
5. Hiển thị: "Account locked. Please contact support."
```

### **Bước 3: Airplane mode bypass**
```
1. Enable airplane mode (ngắt kết nối)
2. Force close app
3. Mở app trong offline mode
4. App không thể check block status từ server
5. Hiển thị alternative flow: "Verify identity with passport"
```

### **Bước 4: Passport verification flow**
```
1. App hiển thị passport scanning interface
2. User có thể scan passport để verify age
3. Nếu verify thành công → account được unlock
4. Tắt airplane mode → app sync trạng thái mới
```

---
