# https://hackerone.com/reports/2388183

**Loại lỗi:** Business Logic / Access Control
**Impact:** User không được phép vẫn tạo được board mới

---

## Tóm tắt ngắn gọn

Admin cấu hình **chỉ một số group được tạo board**.
User **ngoài group được phép** không thể tạo board theo cách chuẩn, **nhưng vẫn tạo được board mới bằng cách clone board có sẵn**.
Nguyên nhân: backend **không enforce quyền khi clone board**, chỉ enforce khi tạo board mới.

---

## Các bước (dễ hiểu)

1. Admin giới hạn quyền tạo board chỉ cho **group2**
2. User1 thuộc **group1** (không được phép)
3. UI:

   * Không thấy nút `+ Add board` ✅
   * Gửi request tạo board → **403 Forbidden** ✅
4. User1 chọn **Clone board** từ board có sẵn
5. Board mới được tạo thành công ❌

---

## 1️⃣ Request đúng → Response đúng (hành vi mong đợi)

### Request (User không có quyền tạo board)

```
POST /nextcloud/apps/deck/boards
```

### Response đúng

```
403 Forbidden
{
  "message": "Creating boards has been disabled for your account."
}
```

👉 Enforce đúng rule “Limit board creation”

---

## 2️⃣ Request bug → Response bug (hành vi sai)

### Request (Clone board – bypass rule)

```
POST /nextcloud/apps/deck/boards/BOARD_ID/clone
```

### Response bug

```
201 Created
{
  "id": 123,
  "title": "Cloned board",
  "owner": "user1"
}
```

👉 **User không được phép tạo board nhưng vẫn có board mới**

---

## Lỗi phụ liên quan (API vs UI mismatch)

* User chỉ có **read-only permission**
* UI:

  * Không thấy `Clone`, `Export`
* API:

```
GET  /nextcloud/apps/deck/boards/38/export
POST /nextcloud/apps/deck/boards/38/clone
```

→ vẫn chạy ❌

➡️ **UI chặn, API không chặn**

---

## Bản chất lỗi

* Clone board = **tạo board mới**
* Backend:

  * Check quyền ở `/boards`
  * ❌ Không check quyền ở `/boards/{id}/clone`
* Business logic sai: *clone ≠ create*

---

## Impact

* Bypass chính sách quản trị
* User trái phép tạo tài nguyên mới
* Có thể lạm dụng để spam / chiếm tài nguyên
