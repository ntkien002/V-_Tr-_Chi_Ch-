# https://hackerone.com/reports/1935628
---

## 💥 **Tóm tắt lỗ hổng**

* Kẻ tấn công **chèn mã HTML (vd: `<img src>` để leak IP)** vào trường email chưa xác nhận.
* Quản trị viên **thủ công xác nhận email** của người dùng trong Admin Panel.
* Hệ thống render hộp thoại `Confirm user` với HTML từ `user.unconfirmed_email`, dẫn đến việc **thực thi HTML injection trong context của GitLab Admin**.
* Không lọc đủ mạnh => có thể **leak IP** hoặc dùng để dụ admin click vào link độc hại.

---

## 🔥 **PoC tấn công**

### 🎯 Điều kiện:

* GitLab self-hosted, phiên bản trước `15.10.2-ee`.
* Cấu hình `Email Confirmation` = `Soft`.
* Quản trị viên xác nhận email thủ công (Manual Confirm).

### 📌 Các bước:

1. Đăng ký user mới (với email hợp lệ).
2. Vào `Profile` → đổi `email` thành dạng:

```plaintext
attacker@example.com<h2><img src="http://attacker.com/track.png">
```

3. Đăng nhập bằng tài khoản admin.
4. Vào `Admin > Users > [Attacker]` → nhấn `Confirm user`.
5. Modal popup chứa HTML được render trực tiếp (thành `<h2>` và ảnh), **trình duyệt gửi request đến `attacker.com`** → kẻ tấn công thu được IP admin.

---


