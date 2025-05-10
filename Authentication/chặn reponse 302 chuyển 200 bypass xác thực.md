# https://hackerone.com/reports/2666323



---

### 🧨 **Lỗ hổng cốt lõi**
 **HTTP Response Manipulation** dẫn đến **bypass authentication** và thực thi hành vi **gửi khiếu nại với tư cách người dùng khác** mà không cần đăng nhập.
- Ứng dụng sử dụng phản hồi `302 Redirect` để điều hướng người dùng chưa đăng nhập khỏi các endpoint nhạy cảm. Nhưng **kẻ tấn công có thể chặn response, sửa mã trạng thái từ `302` sang `200`**, cho phép frontend tiếp tục hiển thị giao diện và gửi dữ liệu như thể user đã được xác thực.


---

Chuẩn, bu nói đúng — **HTTP 302 là response từ phía server**, không phải client có thể tùy ý đổi một cách bình thường. Nhưng trong context báo cáo kia, kỹ thuật được dùng là **man-in-the-middle response tampering**, thường qua proxy như **Burp Suite**, để **giả lập tình huống máy khách (client) nhận về 200 thay vì 302**, từ đó đánh lừa ứng dụng client-side và bypass logic phía frontend.

---


#### 🔧 Cách làm:

1. **Dùng Burp Suite** hoặc **mitmproxy** để intercept response:

   * Bật **Intercept Response** ở Burp.
   * Gửi request như thường đến endpoint `/App/createappeal.aspx`.
   * Khi server trả về response `HTTP/1.1 302 Found`, **đừng forward ngay**.

2. **Sửa nội dung response**:

   * Đổi dòng đầu tiên:

     ```
     HTTP/1.1 302 Found
     ```

     thành:

     ```
     HTTP/1.1 200 OK
     ```
   * Xoá hoặc sửa `Location` header nếu có.
   * Optionally thêm content HTML giả nếu phía frontend kiểm tra nội dung.
   * Forward về trình duyệt.

Để minh họa một ví dụ hoàn chỉnh cho việc **chỉnh sửa mã trạng thái HTTP từ 302 sang 200**, tôi sẽ đưa ra một **request** và **response** mẫu trong trường hợp khai thác này. Bạn có thể thực hiện các bước tương tự trên Burp Suite hoặc công cụ tương tự để "giả mạo" kết quả.

---

### **Ví dụ Request/Response hoàn chỉnh:**

#### **1. Request (của client gửi đến server):**

```http
GET /App/createappeal.aspx HTTP/1.1
Host: example.mil
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.131 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Upgrade-Insecure-Requests: 1
```

#### **2. Response (server trả về 302 trước khi bị chỉnh sửa):**

```http
HTTP/1.1 302 Found
Location: https://example.mil/App/login.aspx
Content-Length: 0
Connection: keep-alive
Cache-Control: no-store
Pragma: no-cache
Expires: 0
Date: Wed, 12 May 2025 15:10:45 GMT
```

Lý do server trả về mã **302 Found** là vì người dùng chưa đăng nhập và yêu cầu phải chuyển hướng tới trang đăng nhập.

---

#### **3. Sau khi thay đổi 302 thành 200:**

**Sửa đổi bằng Burp Suite** (hoặc công cụ MITM khác), chặn response này và thay đổi:

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 356
Connection: keep-alive
Date: Wed, 12 May 2025 15:10:45 GMT

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Create Appeal</title>
</head>
<body>
  <h1>Submit Appeal</h1>
  <form method="POST" action="/App/createappeal.aspx">
    <label for="email">Email Address:</label>
    <input type="email" id="email" name="email" value="victim@example.com" />
    <button type="submit">Submit Appeal</button>
  </form>
</body>
</html>
```

* **Chú ý**: Mặc dù phản hồi hiện tại là **200 OK**, nhưng hệ thống vẫn đang thực thi các hành động mà không cần xác thực người dùng, vì vậy **form submission** có thể được thực hiện với **email giả mạo** (ví dụ: `victim@example.com`).

---

### **Cách thực hiện trên Burp Suite (hoặc công cụ tương tự)**:

1. **Intercept Request**:

   * Gửi một yêu cầu GET tới `/App/createappeal.aspx`.
   * Dừng lại và chỉnh sửa response của server.

2. **Thay đổi Response**:

   * Thay `HTTP/1.1 302 Found` thành `HTTP/1.1 200 OK`.
   * Sửa nội dung HTML nếu cần thiết để nó không yêu cầu xác thực.
   * **Lưu ý**: Đảm bảo server không kiểm tra session hoặc cookie sau khi gửi lại response này.

3. **Forward Response**:

   * Sau khi chỉnh sửa xong, forward response này tới trình duyệt. Trình duyệt sẽ nhận được **200 OK** và hiển thị trang tạo yêu cầu, mặc dù người dùng chưa được xác thực.

---

### **Tóm tắt**:

* **Bước 1**: Tạo request HTTP như bình thường.
* **Bước 2**: Server trả về **302** (chuyển hướng đến trang đăng nhập).
* **Bước 3**: Sửa đổi mã trạng thái HTTP thành **200** để giả mạo thành công và bypass xác thực.
* **Bước 4**: Gửi lại response đã chỉnh sửa, giúp người chưa xác thực tiếp tục thao tác trên hệ thống.
