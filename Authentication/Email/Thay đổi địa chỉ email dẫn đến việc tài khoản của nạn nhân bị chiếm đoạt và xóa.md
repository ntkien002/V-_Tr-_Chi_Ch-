# https://hackerone.com/reports/2587953
### **Lỗ hổng: Email Takeover và Xóa Tài Khoản Vĩnh Viễn**

* **Vấn đề chính**: Kẻ tấn công có thể thay đổi địa chỉ email của mình thành email của nạn nhân (người dùng hiện tại), dẫn đến việc tài khoản của nạn nhân bị chiếm đoạt và xóa vĩnh viễn.
* **Lỗ hổng xác thực**: Hệ thống không có xác thực đúng đắn khi thay đổi địa chỉ email, cho phép kẻ tấn công dễ dàng thay đổi email của mình thành email của nạn nhân.
* **Quá trình tấn công**:

  1. Kẻ tấn công đăng nhập vào tài khoản của mình.
  2. Đăng nhập vào tài khoản của nạn nhân.
  3. Thực hiện thay đổi email từ email của kẻ tấn công thành email của nạn nhân.
  4. Khi thử đăng nhập với tài khoản của nạn nhân, hệ thống sẽ báo lỗi "Thông tin đăng nhập không hợp lệ", cho thấy tài khoản của nạn nhân đã bị chiếm đoạt.
  5. Đổi lại email của kẻ tấn công, và nạn nhân sẽ không thể đăng nhập vào tài khoản nữa. Hệ thống sẽ coi email của nạn nhân là đã bị xóa vĩnh viễn.

