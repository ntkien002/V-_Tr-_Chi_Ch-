# https://hackerone.com/reports/3112106
---

## Môi trường giả lập

* **Admin** và **Member** đều có tài khoản Dust.
* **Agent Gemini** đã bị **Admin** tắt (disabled) trong phần “Quản lý đại lý”.

---

## Bước 1: Xác nhận agent Gemini đã bị tắt

1. **Login** bằng tài khoản Admin.

2. Vào **Quản lý đại lý** (Agent Management). Xác minh trạng thái của `Gemini` là **Disabled**.
   ![Agent Gemini disabled](F4285482)

3. **Logout** Admin.

---

## Bước 2: Tạo cuộc trò chuyện bình thường

1. **Login** bằng tài khoản Member.
2. Tạo mới một **Conversation** → gõ nội dung bất kỳ, chọn agent mặc định (ví dụ “Dust AI” hoặc “Default”).
3. Xác nhận không có tùy chọn nào cho `Gemini` (vì agent này đã disabled).

---

## Bước 3: Bắt và chỉnh sửa request

1. Mở **Burp Suite** (hoặc proxy tương tự), bật intercept.

2. Thao tác trên trang chat, gửi một tin nhắn bất kỳ để có request mẫu (POST).

3. Tìm request tương tự:

   ```
   POST /api/w/:workspace/assistant/conversations/:convId/messages/:msgId/edit
   Content-Type: application/json
   Cookie: …member session…

   {
     "content": ":mention[dust-ai]{sId=dust-ai} Hello",
     "mentions": [{ "type":"agent", "configurationId":"dust-ai" }]
   }
   ```

   ![Bắt request edit](F4285487)

4. **Chỉnh sửa** payload:

   * Thay `dust-ai` → `gemini-pro` (hoặc `gemini`) trong cả `"content"` và `"mentions"`.

   ```json
   {
     "content": ":mention[gemini-pro]{sId=gemini-pro} Bạn khỏe không?",
     "mentions":[{ "type":"agent","configurationId":"gemini-pro" }]
   }
   ```

5. Forward request.

---

## Bước 4: Nhận response từ agent Gemini

* Server **vẫn** xử lý và trả về message từ Gemini, dù agent đã bị disabled.
  ![Phản hồi từ Gemini](F4285491)
* Điều này chứng tỏ permissions trên **API** không đồng bộ với GUI, cho phép bypass quyền Admin.

---

## Kết quả & Impact

* **Bypass mọi hạn chế** do Admin đặt ra: Member vẫn chat được với Gemini.
* Có thể dùng Gemini để thu thập thông tin nội bộ, test, hoặc lạm dụng vượt giới hạn cấp phép.

---


