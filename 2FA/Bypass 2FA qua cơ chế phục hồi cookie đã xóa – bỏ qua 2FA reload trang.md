# https://hackerone.com/reports/2315420
---

### 💥 **Lỗ hổng:** Bypass 2FA qua cơ chế phục hồi cookie đã xóa – bỏ qua 2FA reload trang

**Trang bị:** Chỉ cần username + password đúng

---

### 🔍 **Chi tiết kỹ thuật:**

1. Khi login đúng username/password, app chuyển sang bước 2FA.
2. Tại bước 2FA, server **đã set full session cookies** gồm `PHPSESSID`, `bb_sessionhash`, và đặc biệt là `bb_refresh`.
3. Nếu attacker **xóa cookie `bb_refresh`**, sau đó **F5 (reload)** trang thì...
   👉 **Session được restore như đã login hoàn tất**, **bỏ qua 2FA hoàn toàn**.

---

### 🧪 **PoC thực hiện:**

```plaintext
1. Đăng nhập bình thường (đúng username/pass).
2. Trình duyệt chuyển đến trang 2FA.
3. Mở DevTools → tab Application → Storage → Cookies.
4. Xoá cookie: bb_refresh.
5. Reload lại trang → vào dashboard luôn, không cần nhập OTP.
```

---

### ⚠️ **Nguyên nhân chính:**

* App xử lý phiên (session) chưa chuẩn:

  * Đặt session đầy đủ trước khi xác thực OTP.
  * `bb_refresh` acting như một cơ chế điều kiện tạm thời cho việc duy trì trạng thái 2FA.
  * Khi xóa nó → app fallback sang trạng thái **“đã login”** thay vì “chưa xác thực”.

