# https://hackerone.com/reports/2588329
---

## 🔥 **Tóm tắt lỗi**

Lỗi nằm ở **quy trình xác minh OTP khi đổi số điện thoại** trong ứng dụng *inDrive*. Ứng dụng **chấp nhận mã OTP cố định `0000`** như một mã hợp lệ, dẫn đến:

* Bất kỳ ai cũng có thể thay đổi số điện thoại tài khoản thành số tùy ý.
* Dễ dàng **chiếm đoạt tài khoản**, xoá tài khoản, huỷ đơn hàng, thực hiện hành vi phá hoại hoặc gián đoạn dịch vụ.

---

## ⚔️ **PoC tấn công**

1. Đăng nhập vào tài khoản inDrive (có thể là tài khoản phụ).
2. Vào **Cài đặt ➝ Đổi số điện thoại**.
3. Nhập số điện thoại mới (mục tiêu muốn chiếm đoạt).
4. Khi được yêu cầu nhập mã OTP, gõ `0000`.
5. ✅ Số điện thoại **được cập nhật thành công**, không cần quyền kiểm soát số đó.

---

## 🎯 **Impact**

* **Tấn công chiếm đoạt tài khoản hàng loạt**, chỉ cần đoán được số điện thoại.
* **Xoá tài khoản nạn nhân, thay đổi thông tin, truy cập dữ liệu cá nhân, lịch sử chuyến đi.**
* Kết hợp với kỹ thuật **SIM Swap**, mức độ thiệt hại sẽ tăng cao.
* **Rủi ro rất cao cho doanh nghiệp** nếu bị khai thác có chủ đích.

