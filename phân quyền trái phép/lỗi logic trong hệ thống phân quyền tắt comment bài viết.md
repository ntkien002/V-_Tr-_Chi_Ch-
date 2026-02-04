


---

## Các bước (dễ hiểu)

1. **Account A** tạo bài viết (comment = enabled)
2. **Account B** comment vào bài viết
3. **Account A** tắt comment của bài viết
4. **Account B** tiếp tục gửi comment mới
5. **Kết quả:** Comment vẫn được chấp nhận ❌ (đáng ra phải bị chặn)

---

## Bản chất lỗi

* **Improper Access Control / Business Logic Flaw**
* Backend **không kiểm tra lại trạng thái `comments_enabled=false`**
* Chỉ kiểm tra: *user đã từng comment → cho phép tiếp tục*
