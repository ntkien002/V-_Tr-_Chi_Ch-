# https://hackerone.com/reports/2322663
---

## 1. Mô tả lỗ hổng

* **Thể loại**: IDOR (Insecure Direct Object Reference) – Tăng đặc quyền ngang hàng
* **URL liên quan**:

  * Giao diện người dùng: `https://mtn.ng/offers/`
  * API nội bộ hiển thị chi tiết gói cước khi click “Ưu đãi SMS”
* **Cơ chế**: Khi người dùng đã xác thực OTP và nhìn thấy danh sách gói cước, attacker có thể can thiệp DevTools để sửa đổi nội dung hiển thị (text DOM) trước khi bấm “Ưu đãi SMS”. Server sẽ gửi tin nhắn SMS dựa trên dữ liệu đã bị sửa đổi dù client-side chỉ là thao tác giả mạo DOM, dẫn đến gửi SMS theo nội dung attacker muốn tới nạn nhân.

---

## 2. Các bước tái tạo

1. **Xác thực**

   * Truy cập `https://mtn.ng/offers/`.
   * Nhập số điện thoại, nhận và nhập OTP, click **“Xác thực”** → bảng gói cước hiển thị.

2. **Giả mạo DOM**

   * Scroll đến một gói cước (ví dụ “Data4ME Bundles 4Me (2)”), click chuột phải → **Inspect**.
   * Thay đổi nội dung text hoặc thuộc tính HTML trong DevTools (ví dụ đổi tên gói, giá tiền, nội dung SMS).

3. **Gửi SMS**

   * Đóng DevTools, click **“Ưu đãi SMS”**.
   * Server gửi SMS đến số đã xác thực, nhưng nội dung SMS là dữ liệu attacker đã chỉnh sửa trên client.

---

--

## 4. Impact

* Dù attacker không thể thay đổi số điện thoại, họ **toàn quyền kiểm soát** nội dung SMS gửi đến nạn nhân.
* Có thể lợi dụng để spam SMS, phishing hoặc SMS bombing dưới tên “MYMTN”.

