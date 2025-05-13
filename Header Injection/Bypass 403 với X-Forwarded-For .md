# https://hackerone.com/reports/1224089

Lỗ hổng trong báo cáo này là một **bypass hạn chế truy cập IP** dựa vào tiêu đề **`X-Forwarded-For`**, thường dùng trong reverse proxy (như Nginx) để nhận biết IP gốc của client.

---

### 🎯 **Tóm tắt PoC và bản chất khai thác**

* Truy cập trực tiếp `/nginx_status/` bị trả về HTTP **403 Forbidden**.
* Gửi kèm tiêu đề:

  ```
  X-Forwarded-For: 127.0.0.1:80
  ```

  \=> Server phản hồi **200 OK**, tiết lộ nội dung `/nginx_status/`.

📌 Tức là Nginx đang tin tưởng địa chỉ trong `X-Forwarded-For` để xác định xem client có phải là "localhost" hay không.

---

### 🚀 **Kịch bản khai thác thực tế**

Giả định Acronis có một location được cấu hình như sau:

```nginx
location /nginx_status {
    allow 127.0.0.1;
    deny all;
}
```

→ Sai lầm thường gặp: đặt Nginx phía sau một reverse proxy (hoặc không cấu hình chính xác `real_ip_header`) nhưng **vẫn tin tưởng `X-Forwarded-For` từ bất kỳ client nào**.

Kẻ tấn công có thể gửi:

```http
GET /nginx_status HTTP/1.1
Host: branded-us4-cloud.acronis.com
X-Forwarded-For: 127.0.0.1
```

→ Vượt qua whitelist.

