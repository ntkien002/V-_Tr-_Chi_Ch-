
- **Tình huống**:  
  Tenant A và Tenant B đều sử dụng Stripo AI Hub để quản lý các chiến dịch email.  
  Tenant A có một dự án (Project X) đã bị xóa khỏi hệ thống.  

- **Khai thác**:  
  - Kẻ tấn công trong Tenant B tìm cách tham chiếu đến **ID của Project X** (dù đã bị xóa).  
  - Do lỗi xử lý, hệ thống vẫn cho phép truy cập dữ liệu liên quan đến Project X.  
  - Kết quả: Tenant B có thể xem hoặc lấy dữ liệu vốn thuộc Tenant A.  

- **Ví dụ minh họa**:  
  - Tenant A có chiến dịch email chứa danh sách khách hàng VIP.  
  - Sau khi dự án bị xóa, ID dự án vẫn tồn tại trong một số API hoặc liên kết nội bộ.  
  - Tenant B gửi yêu cầu API với ID đó.  
  - Hệ thống không kiểm tra đúng quyền hạn và trả về dữ liệu khách hàng VIP của Tenant A.  

- **Hậu quả**:  
  - Tenant B có thể truy cập thông tin nhạy cảm (email, nội dung chiến dịch, dữ liệu khách hàng).  
  - Vi phạm nguyên tắc cách ly dữ liệu giữa các tenant.  
  - Nguy cơ rò rỉ dữ liệu, mất uy tín và vi phạm pháp lý.  

