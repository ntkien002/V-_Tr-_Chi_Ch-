# https://hackerone.com/reports/1709881
---

## 💥 **Mục tiêu**:

Chiếm quyền truy cập bất kỳ tài khoản nào **mà không cần biết mật khẩu**.

## 📌 **Mô hình lỗ hổng**:

Ứng dụng **đặt quá nhiều niềm tin vào dữ liệu từ phía client** mà không xác thực đúng cách ở phía server — classic lỗi Broken Authentication + Insecure Direct Object Reference (IDOR)/Logic flaw.

---

## 🧠 **Cơ chế hoạt động của bug**:

Trong JSON request gửi đến endpoint `/app/login`, client có thể sửa đổi payload như sau:

```json
{
  "params": {
    "update": [
      {
        "param": "user",
        "value": {
          "userEmail": "victim@example.com",
          "userPassword": "wrong-password"
        },
        "op": "one"
      },
      {
        "param": "gateway",
        "value": true,
        "op": "one"
      }
    ]
  }
}
```

👉 Nếu `value: true` ở `param: gateway` được set, server **tin rằng quá trình xác thực đã hoàn tất**, **bỏ qua check password**, và cho login thành công.

---

## 🧪 **POC chi tiết – Các bước tái hiện**:

1. **Tạo 2 tài khoản**:

   * `attacker@example.com`
   * `victim@example.com`

2. **Dùng Burp Suite**, đăng nhập bằng tài khoản attacker.

   * Ghi lại HTTP request gửi đến `/app/login`.

3. **Sửa payload trong Burp Repeater**:

   * Thay `userEmail` thành email của victim.
   * Giữ nguyên `userPassword` (có thể sai).
   * Sửa `"gateway": false` thành `"gateway": true`.

4. **Gửi request sửa đổi → được trả về HTTP 200 và phản hồi như đang login vào tài khoản victim**.

---

## 🔍 **Phân tích kỹ thuật**:

| Thành phần    | Vấn đề                                                                 |
| ------------- | ---------------------------------------------------------------------- |
| Backend Logic | Tin tưởng `gateway=true` → bỏ qua logic kiểm tra mật khẩu              |
| Authorization | Không có kiểm tra quyền hay token hợp lệ khi chuyển context người dùng |
| Input Trust   | Không có bất kỳ validate nào cho `userEmail` trước khi bind session    |

---

## 💣 **Tác động**:

* Chiếm tài khoản bất kỳ người dùng nào.
* Tự động hoá chiếm đoạt hàng loạt với danh sách email.
* Thực hiện thay đổi profile, thay mật khẩu, xóa tài khoản.
* **Rò rỉ dữ liệu cá nhân**, chiếm quyền admin nếu biết email.

---
