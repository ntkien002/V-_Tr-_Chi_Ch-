# https://hackerone.com/reports/2798380
---

#### 🚨 **Tóm tắt nhanh:**

Lỗ hổng nằm ở **quy trình xác minh email của HackerOne** – hệ thống sẽ **tự động xác minh địa chỉ email nếu liên kết xác minh được mở bởi bot quét email**, *mà không cần người dùng tương tác*.

---

#### 🧠 **Cách thức khai thác (PoC):**

1. **Đăng ký** tài khoản HackerOne bình thường.
2. Truy cập phần **thay đổi email** → nhập địa chỉ email thuộc về tổ chức có sử dụng dịch vụ **quét liên kết trong email tự động** (thường là các tổ chức dùng bảo mật email nâng cao).
3. Khi HackerOne gửi mail xác minh → **bot của tổ chức nạn nhân mở link xác minh**, địa chỉ email sẽ **tự động được xác thực**.
4. Sau đó:

   * Tài khoản của attacker giờ đây có thể **sử dụng email công ty giả mạo**, bypass một số xác thực như PullRequest.com.
   * Có thể tạo **tài khoản mang danh công ty** để lừa đảo, truy cập tài nguyên nội bộ nếu hệ thống khác tin tưởng domain đó.

---

### 🧪 **Một số email mẫu có thể thử nghiệm:**

1. `security@security.corp.com`
   → Dễ bị quét nếu tổ chức dùng hệ thống email bảo mật.

2. `alerts@security.com`
   → Các bot thường quét các email dạng thông báo (alert).

3. `noreply@internal.security.com`
   → Nếu bot không kiểm tra domain gửi đến là "noreply", vẫn có thể quét.

4. `it-support@organization.org`
   → Địa chỉ được nhiều hệ thống theo dõi để phân tích link/tiệp đính kèm.

5. `abuse@company.com`
   → Một số hệ thống phản hồi link/tập tin gửi đến abuse để phân tích phishing.
