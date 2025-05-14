
---

## 🔥 **Tóm tắt khai thác**

* **Mục tiêu**: Lỗ hổng **Command Injection** thông qua các tham số `item_image_small` và `item_image_large` trong URL.
* **Kỹ thuật**:

  * Kẻ tấn công có thể chèn các **siêu ký tự shell** vào đường dẫn tải hình ảnh.
  * Nếu hệ thống không lọc đúng, những ký tự này có thể bị **hệ thống xử lý như một phần của lệnh shell**, dẫn đến việc thực thi mã shell tùy ý.

---

## 🧠 **Phân tích kỹ thuật**

### ⚠️ **Kỹ thuật khai thác**:

* **Command Injection** xảy ra khi các tham số không được kiểm tra đầu vào đúng cách và được sử dụng trực tiếp trong lệnh shell.
* Kẻ tấn công có thể chèn các ký tự shell đặc biệt (`;`, `&&`, `|`, `$(...)`) vào tham số URL, gây ra việc **thực thi lệnh tùy ý**.

### 🕵️‍♂️ **Các bước khai thác**:

1. Xác định tham số **`item_image_small`** hoặc **`item_image_large`** trong URL.
2. Thử chèn các ký tự shell như `;`, `&&`, `|`, hoặc các chuỗi như `$(...)` vào trong đường dẫn tải hình ảnh.
3. Nếu lỗ hổng tồn tại và không được lọc, các lệnh shell có thể được thực thi trên hệ thống.

Ví dụ về khai thác:

```bash
https://partner.steamgames.com/apps/communityitems/?item_image_small=path/to/image;ls%20-la
```

Trong đó, lệnh `ls -la` sẽ được thực thi nếu lỗ hổng tồn tại.

---

## 💣 **PoC hoàn chỉnh**

```bash
https://partner.steamgames.com/apps/communityitems/?item_image_large=path/to/image;echo%20'Vulnerable%20Server'
```

Kết quả: Lệnh `echo 'Vulnerable Server'` sẽ được thực thi nếu lỗ hổng này chưa được vá.

---

