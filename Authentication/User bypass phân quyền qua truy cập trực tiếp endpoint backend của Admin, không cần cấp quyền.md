# https://hackerone.com/reports/1457471
---

## 🧠 **Mô tả lỗi**

Hệ thống UI của Shopify Partner Portal đã **ẩn chức năng tạo Referral (POS Lead)** khỏi user không có quyền, **nhưng API backend vẫn chấp nhận request từ các user này**, dẫn tới **Privilege Escalation**.

---

## 🔨 **Các bước khai thác chi tiết (PoC)**

### ✅ **Bước 1: Tạo user có quyền admin**

* Truy cập: `https://partners.shopify.com/`
* Đăng nhập với tài khoản admin (hoặc owner).

### ✅ **Bước 2: Tạo user với quyền giới hạn (limited privileges)**

* Từ giao diện admin, mời một user mới và gán role **KHÔNG CÓ quyền `View referrals`**.
* VD: chỉ có quyền quản lý apps.

→ Xác thực bằng hình ảnh (như trong report `limited_user.png`): user được tạo không có quyền referral.

---

### ✅ **Bước 3: Đăng nhập bằng user bị giới hạn quyền**

* Truy cập đường dẫn quản lý referral:

  ```
  https://partners.shopify.com/<partner_id>/referrals/
  ```
* Kết quả mong đợi: Giao diện báo lỗi **“Access Denied”** hoặc không hiển thị gì.

---

### ✅ **Bước 4: Truy cập trực tiếp endpoint backend tạo POS Lead**

* Truy cập URL sau:

  ```
  https://partners.shopify.com/<partner_id>/partner_leads/pos
  ```
* Kết quả: **Trang form tạo POS Lead hiện ra** dù user không có quyền.

---

### ✅ **Bước 5: Submit form**

* Nhập thông tin đầy đủ: tên, công ty, email, v.v.
* Submit form.

→ ✅ **Dữ liệu POS Lead được gửi thành công**, dù user không có quyền hợp lệ.

---

