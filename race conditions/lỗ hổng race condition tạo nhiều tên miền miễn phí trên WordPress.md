
---

## 🧠 **Tóm tắt lỗ hổng race condition tạo nhiều tên miền miễn phí trên WordPress (Gravatar)**

### 💥 **Mô tả:**

Lỗ hổng thuộc loại **Logic Business / Race Condition** trong quá trình người dùng Gravatar liên kết và đăng ký **tên miền miễn phí** với tài khoản WordPress. Bằng cách gửi song song nhiều yêu cầu đến API xử lý thanh toán, kẻ tấn công có thể tạo **nhiều tên miền miễn phí thay vì chỉ 1**, phá vỡ giới hạn do nhà cung cấp đặt ra.

---

### 🧪 **Các bước tái hiện (PoC):**

1. **Tạo tài khoản Gravatar miễn phí** tại:
   [https://en.gravatar.com](https://en.gravatar.com)

2. **Đăng nhập**, sau đó truy cập vào phần:
   **"My Profile" → Chọn tùy chọn “Get a Free Custom Domain”**

3. Sau khi nhấp vào tùy chọn trên, người dùng được chuyển hướng tới:

   ```
   https://wordpress.com/start/domain-for-gravatar/domain-only?search=yes&new=examplegravatar.com
   ```

4. Tiến hành đăng ký tên miền, tiếp tục quá trình thanh toán **cho đến khi nhận được endpoint sau**:

   ```
   https://public-api.wordpress.com/rest/v1.1/me/transactions
   ```

5. **Tại thời điểm này**, attacker sử dụng công cụ như **Burp Repeater / Intruder / script Python multi-thread** để:

   * **Gửi đồng thời (parallel)** 15 request `POST` đến endpoint trên
   * **Mỗi request** thay đổi giá trị trong trường `"meta"` như:

     ```json
     {
       "meta": {
         "domain_label": "gravatar-1" // hoặc gravatar-2, gravatar-3,...
       }
     }
     ```

6. Gửi toàn bộ các request **gần như đồng thời**, server xử lý race condition khiến nhiều tên miền khác nhau được cấp phát, **bỏ qua hạn mức 1 domain/user**.

---

### 📌 **Kết quả:**

* Kẻ tấn công sở hữu **nhiều tên miền miễn phí** thay vì bị giới hạn chỉ 1.
* Gây tổn thất tài nguyên và sai lệch logic thanh toán nội bộ của hệ thống WordPress.
uốn bu viết lại script khai thác thực tế không? (dùng Python hoặc Burp Macro tùy cách bu cần)
