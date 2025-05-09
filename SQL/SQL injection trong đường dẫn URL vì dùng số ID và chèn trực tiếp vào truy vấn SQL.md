# https://hackerone.com/reports/2633959
---
- Ứng dụng https://corporate.admyntec.co.za/ có SQL injection trong đường dẫn URL vì nó lấy số ID ở đó và chèn trực tiếp vào truy vấn SQL ở phía sau mà không khử trùng chúng. Trong quá trình đăng ký, số ID người dùng (Hộ chiếu hoặc CMND), số Tổ chức được yêu cầu, cũng như các tài liệu có liên quan. Tất cả những thông tin này đều được lưu trữ trong Cơ sở dữ liệu phía sau.

-
- 1. URL: `https://corporate.admyntec.co.za/customerInsurance/newCustomerStep8/userId/868878/customerId/732562'/contactPersonId/0  `
  2. image
 
  ![image](https://github.com/user-attachments/assets/c5c735d9-fbe4-4c7c-b55a-326957a13657)
