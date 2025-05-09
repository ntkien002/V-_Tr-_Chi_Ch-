# API-Pentest
---

Nếu API chỉ có endpoint kiểu:

```
GET /user/profile
```

và **không có ID tham số** như `/user/123/profile`, thì về lý thuyết là nó **bảo mật hơn** với IDOR, **nhưng vẫn có thể bị lỗi logic**. Dưới đây là các hướng kiểm tra tiếp theo, **không IDOR nhưng vẫn có lỗ**.

---

## 🕳️ 1. **Kiểm tra token gắn nhầm**

> Nếu app dùng `Authorization: Bearer <token>` → kiểm tra xem token có bị **gán sai** hay **dùng lại được cho account khác không**.

* Dùng token A → login user A.
* Gán token A cho user B → app có trả profile của B không?
* Xem token có expiration logic rõ ràng chưa?

---

## 🔄 2. **Session Confusion**

* Nếu bạn login account A, sau đó đổi cookie/token session sang account B (nếu có refresh token hoặc SSO), nhưng **API `/user/profile` vẫn trả về thông tin của A**, thì backend lưu session sai → lỗi logic.

---

## 📤 3. **Gửi request với role khác**

* Giả sử bạn có 2 account: `user` và `moderator`
* Gửi request `/user/profile` bằng token `user`, rồi đổi sang token `moderator` → có thấy thông tin user khác?

---

## 🧠 4. **Thử override header**

* API có thể dựa trên header như `X-User-Id`, `X-Forwarded-For`, `X-Auth-Token` → thử fake để bypass.

```http
GET /user/profile
X-User-Id: 5
X-Auth-Token: abc
```

→ nếu trả về thông tin user khác → logic bị override từ header thay vì token xác thực.

---

## 🔀 5. **Swap Token Replay**

* Lấy token ở thời điểm đăng nhập đầu tiên.
* Logout user.
* Gửi lại token cũ → nếu vẫn lấy được `/user/profile` → lỗi về token revocation (có thể là lỗ hổng logic session management).

---

## 🔐 6. **Test với token giả / malformed**

* Gửi token sai định dạng hoặc không hợp lệ → API xử lý như thế nào?
* Một số API không validate token đúng chuẩn JWT hoặc có logic fallback nếu token sai.

---

## 🎯 7. **Race Condition / Timing Attack**

* Gửi 2 request song song đến `/user/profile` và `/user/update`
* Thử cập nhật thông tin sau đó xem response profile có bất nhất không.

---
