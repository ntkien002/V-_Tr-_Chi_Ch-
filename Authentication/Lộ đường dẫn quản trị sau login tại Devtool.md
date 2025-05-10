# https://hackerone.com/reports/2342461

---

## 1. Mô tả lỗ hổng

* **Thể loại**: Kiểm soát truy cập không đúng cách (Broken Access Control)
* **URL liên quan**:

  * Trang công khai: `https://nin.mtn.ng/`
  * Đường dẫn ẩn lộ: `../wp-admin/admin-ajax.html`
* **Cơ chế**: Khi người dùng click “Kiểm tra trạng thái liên kết NIN của bạn” rồi mở DevTools → Inspect, request AJAX hiển thị endpoint quản trị (`/wp-admin/admin-ajax.html`) mà không cần xác thực, cho phép đọc thông tin nhạy cảm hoặc thực thi hành vi trái phép.

---

## 2. Các bước tái tạo

1. **Truy cập trang**

   * Mở `https://nin.mtn.ng/`.

2. **Kích hoạt chức năng NIN**

   * Click nút **“Kiểm tra trạng thái liên kết NIN của bạn”**.

3. **Mở DevTools**

   * Nhấp chuột phải vào thanh header (màu vàng MTN) → chọn **Inspect**.

4. **Quan sát request**

   * Trong tab **Network**, quan sát request AJAX đến `../wp-admin/admin-ajax.html` mà không cần token hay session của admin.

---
