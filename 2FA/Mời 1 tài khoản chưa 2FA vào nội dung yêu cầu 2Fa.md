
---

### 🧠 **Phân tích nhanh lỗi:**

#### 🎯 **Mục tiêu bảo mật bị vi phạm**

* Tổ chức **bật chế độ bắt buộc 2FA** cho tất cả researcher để **đảm bảo rằng chỉ người dùng được bảo vệ 2FA mới được truy cập báo cáo nhạy cảm**.
* Tuy nhiên, một user bật 2FA có thể **mời người khác chưa bật 2FA** vào báo cáo => **gián tiếp cấp quyền truy cập không đúng chính sách bảo mật**.

#### ⚙️ **Cách khai thác (PoC):**

1. Tạo 2 tài khoản A (đã bật 2FA) và B (chưa bật).
2. Dùng A tạo báo cáo trong một chương trình yêu cầu 2FA.
3. A thêm B làm collaborator trong báo cáo.
4. B chấp nhận lời mời và **truy cập được nội dung báo cáo dù không bật 2FA**.

#### 🧨 **Tác động:**

* **Vi phạm chính sách bảo mật** của tổ chức.
* Cho phép **truy cập trái phép vào dữ liệu nhạy cảm** trong báo cáo bug bounty.
* **Vô hiệu hóa mục tiêu chính của 2FA**: ngăn chặn truy cập nếu tài khoản bị compromise.

---

