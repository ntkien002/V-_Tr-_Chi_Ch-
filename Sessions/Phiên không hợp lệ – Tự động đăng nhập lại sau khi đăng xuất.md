https://hackerone.com/reports/3101207
---

## 1. Môi trường

* Trình duyệt: Chrome hoặc Firefox.
* Workspace Dust hoạt động bình thường, có sẵn nút **Đăng xuất**.

---

## 2. Bước 1 – Đăng nhập như bình thường

1. Mở trang [https://dust.tt](https://dust.tt)
2. Đăng nhập bằng tài khoản hợp lệ.
3. Xác nhận bạn vào được dashboard/workspace.

---

## 3. Bước 2 – Đăng xuất

1. Nhấn **Đăng xuất**.
2. UI chuyển về trang login, hiển thị form nhập email/mật khẩu.

> Lúc này về mặt giao diện, người dùng đã “cảm giác” là phiên bị vô hiệu.

---

## 4. Bước 3 – Tái tạo phiên cũ

Không làm gì đặc biệt, giữ nguyên tab hoặc mở tab mới cùng trình duyệt, truy cập lại:

```
https://dust.tt/sign-in
```

hoặc nhấn nút **Đăng nhập** trên giao diện.

**Kết quả thực tế:**

* Ứng dụng **tự động redirect** bạn vào workspace, mà **không yêu cầu** nhập email/mật khẩu nào cả.
* Bạn “bỗng nhiên” được đăng nhập lại, session cũ vẫn còn hiệu lực  chứng tỏ token/cookie chưa bị xoá hoặc server chưa thu hồi.

---

## 5. Bước 4 – Quan sát cookie & cache

Mở DevTools → Application → Cookies:

* Cookie phiên (`appSession`) **vẫn còn nguyên** sau logout.
* Server không gửi header `Set-Cookie: appSession=; Max-Age=0` để xoá.
* Trang login không có header cache-control/ngăn lưu cache → trình duyệt có thể cache trang đã auth.

---

## 6. Impact

* **Đăng xuất vô dụng**: ai có access vào máy tính (quán net, thư viện…), chỉ cần 1 click “Đăng nhập” lại là xong.
* Thiếu bảo vệ cho **thiết bị chia sẻ hoặc bị mất cắp**.
* Gây hiểu lầm: user tưởng đã an toàn nhưng thực ra session vẫn hoạt động.

---
