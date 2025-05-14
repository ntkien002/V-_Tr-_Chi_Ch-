1. Tạo báo cáo (hoặc sử dụng báo cáo hiện có):

Hacker tạo một báo cáo bug mới, hoặc sử dụng báo cáo có sẵn. Báo cáo có thể chứa các tệp đính kèm.

2. Truy cập tính năng tóm tắt báo cáo:

Sau khi báo cáo được tạo, kẻ tấn công vào phần chỉnh sửa tóm tắt báo cáo.

3. Bắt và thay đổi ID tệp đính kèm trong yêu cầu HTTP:

Kẻ tấn công sử dụng công cụ như Burp Suite để chặn (intercept) yêu cầu HTTP khi chỉnh sửa báo cáo.

4. Lúc này, trong yêu cầu HTTP (ví dụ: PUT request), sẽ có phần chứa các ID tệp đính kèm.

Kẻ tấn công thay đổi ID của tệp đính kèm của chính mình thành ID của tệp đính kèm thuộc về nạn nhân (có thể tìm thấy ID tệp này thông qua các hành động trước đó hoặc dò tìm).

5. Gửi yêu cầu đã thay đổi:

Sau khi thay đổi ID tệp đính kèm thành ID tệp của nạn nhân, hacker gửi lại yêu cầu.

6. Xem tệp của nạn nhân:

Nếu lỗ hổng tồn tại, yêu cầu sẽ được chấp nhận và kẻ tấn công sẽ có quyền truy cập vào tệp của người dùng khác mà không cần sự cho phép của họ.
