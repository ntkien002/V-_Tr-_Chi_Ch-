# https://hackerone.com/reports/1675516
---

### 🎯 **Tóm tắt bản chất lỗ hổng**

**Vị trí**: Chức năng nhắn tin của `mercadolibre.com.ar`
**Loại lỗi**: Stored XSS
**Cơ chế bypass**: Lạm dụng nhiều thẻ `<p>` chưa đóng để làm sai lệch trình khử trùng (sanitizer) → cho phép inject thẻ tùy ý.

---

### 🔥 **Payload PoC cơ bản**

```html
<p> <p> <p> <p> <p> <p> <p> <p> <audio/src/onerror=alert(document.domain)>
```

**Ý tưởng**:

* Trình sanitizer tưởng rằng đang xử lý các thẻ `<p>` hợp lệ.
* Nhưng do số lượng `<p>` quá lớn và không đóng, nó *vỡ layout nội bộ*, và cho phép **bỏ lọt một thẻ độc hại** (`<audio>`) mà không bị encode lại hoặc loại bỏ.
* Cái này tương tự lỗi **tag confusion / nesting misparsing** khi dùng các sanitizer yếu hoặc tự viết.

---

