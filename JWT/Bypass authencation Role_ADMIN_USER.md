
---

## 1. Authentication Bypass (JWT Realm Confusion)

### Nguyên nhân gốc

* Hệ thống dùng **JWT** để xác thực
* JWT có field `realm` để phân biệt:

  * `test-user` → user thường
  * `test-dashboard` → admin panel
* **Backend tin tưởng realm do client gửi lên**

### Khai thác

* Attacker login user thường
* Chỉnh request login:

```json
{
  "email": "user@gmail.com",
  "password": "xxx",
  "realm": "test-dashboard"
}
```

* Server **không validate realm**
* Trả về JWT hợp lệ cho admin panel

👉 **Logic flaw / Broken Authentication**

---


