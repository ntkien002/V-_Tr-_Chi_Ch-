# https://hackerone.com/reports/2542372
---

### 🎯 **Mục tiêu**

Chiếm đoạt tài khoản người dùng MTN chỉ với số điện thoại thay thế (`alternative linked number`) – không cần xác thực chính chủ.

---

### 🔓 **Tổng quan lỗ hổng**

* **Lỗi logic xác thực số thay thế (alternative MSISDN)**
* Hệ thống backend chỉ xác thực OTP gửi tới **số thay thế**, nhưng sau khi xác thực thì cấp quyền truy cập **vào số chính**.
* Có thể tuỳ ý truyền số điện thoại thay thế vào request login JSON như:

```json
{
  "msisdn": "2348144091760|2347033412902"
}
```

> Trong đó `2348144091760` là số chính (nạn nhân), `2347033412902` là số thay thế giả mạo của attacker.

---

### 🧪 **PoC Tái tạo tấn công**

1. Gửi request đến API login của MTN với số điện thoại `msisdn` chứa dấu `|` để ép backend chấp nhận số thay thế:

   * `POST /api/auth/login`
   * Body:

     ```json
     {
       "msisdn": "2348144091760|2347033412902"
     }
     ```

2. OTP được gửi đến số `2347033412902` (do attacker sở hữu).

3. Sau khi nhập OTP thành công, attacker được đăng nhập vào **tài khoản của số chính** `2348144091760`.

