# https://hackerone.com/reports/2319586
---

## 1. Mô tả lỗ hổng

* **Thể loại**: Broken Access Control (Tăng đặc quyền ngang hàng)
* **URL liên quan**:

  * Giao diện người dùng: `https://mtn.ng/offers/`
  * API hiển thị offers: `https://mtn.ng/offers/list?phone=<số_điện_thoại>`
* **Cơ chế**: Sau khi xác thực bằng OTP với số điện thoại của kẻ tấn công, attacker có thể đổi tham số `phone` trong URL thành số điện thoại bất kỳ của nạn nhân mà không bị chặn, từ đó xem và thao tác dữ liệu của nạn nhân.

---

## 2. Các bước tái tạo

1. **Bước 1**: Truy cập `https://mtn.ng/offers/`

   * Nhập số điện thoại của kẻ tấn công, click **“Gửi”**.
   * Nhập mã OTP nhận được, click **“Xác thực”**.

2. **Bước 2**: Khi xác thực thành công, UI tự động load bảng offers từ endpoint:

   ```
   https://mtn.ng/offers/list?phone=<số_đã_xác_thực>
   ```

3. **Bước 3**: Thay đổi trực tiếp tham số `phone` trong URL thành số điện thoại của nạn nhân (ví dụ `2349138557692`)

   ```
   https://mtn.ng/offers/list?phone=2349138557692
   ```

   → Thông tin offers của nạn nhân được trả về mà không cần OTP.

---

---

## 4. Impact

* Kẻ tấn công có thể **xem** và **thao tác** dữ liệu nhạy cảm của bất kỳ khách hàng nào (OTP không kiểm soát mức độ access).
* Có thể đăng ký dịch vụ, xem lịch phát sóng, hoặc gửi tin nhắn trá hình MTN tới nạn nhân.

