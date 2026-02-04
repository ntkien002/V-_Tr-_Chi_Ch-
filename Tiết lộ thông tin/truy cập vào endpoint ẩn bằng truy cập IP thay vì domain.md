# https://hackerone.com/reports/2473173

**Loại lỗi:** Information Disclosure / Misconfiguration

---

## Tóm tắt ngắn gọn

Truy cập **server pixiv bằng IP trực tiếp** cho phép mở endpoint **`/server-status`** (Apache mod_status) vốn chỉ dành cho admin nội bộ. Endpoint này làm lộ **thông tin hệ thống và request nội bộ**.

---

## Các bước (dễ hiểu)

1. Truy cập pixiv **qua domain** → không thấy `/server-status`
2. Truy cập **qua IP trực tiếp** `203.137.128.240`
3. Mở `/server-status`
4. **Kết quả:** Thấy thông tin nội bộ server ❌

---

## 1️⃣ Request đúng → Response đúng (hành vi mong đợi)

### Request (qua domain)

```
GET https://www.pixiv.net/server-status
```

### Response đúng

```
403 Forbidden
```

hoặc

```
404 Not Found
```

👉 Endpoint admin **không được public**

---

## 2️⃣ Request bug → Response bug (hành vi sai)

### Request (qua IP trực tiếp)

```
GET https://203.137.128.240/server-status
```

### Response bug

```
200 OK
Content-Type: text/html

Apache Server Status for ...
Server Version: Apache/...
Current Time: ...
CPU Usage: ...
Active Requests:
- GET /api/...
- POST /login
...
```

👉 **Lộ thông tin nội bộ server & traffic runtime**

---

## Tác động

* Lộ **endpoint nội bộ, pattern request**
* Hỗ trợ attacker **recon / lateral analysis**
* Rủi ro kết hợp với lỗi khác (SSRF, RCE, Auth bypass)

---

## Nguyên nhân gốc

* **VirtualHost / Access Control sai**
* `/server-status` **chỉ giới hạn theo domain**, không giới hạn theo IP
* Thiếu rule:

  * `Require ip 127.0.0.1`
  * `Require local`

---
