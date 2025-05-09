# https://hackerone.com/reports/3102890

---

## 1. Thiết lập ban đầu

1. Tạo một **workspace** mới trên Dust bằng tài khoản Free (giới hạn 3 slot).
2. Xác nhận workspace của bạn đang sử dụng gói miễn phí, cho phép tối đa **3 thành viên**.

---

## 2. Mời trước quá số slot cho phép

1. Trong **Workspace Settings → Members**, nhấn **Invite** và gửi lời mời cho **3 địa chỉ email** (A, B, C).
2. Tiếp tục gửi thêm lời mời cho email **D, E, F…** (bất kỳ số lượng) dù đã đủ 3 slot.

   * Hệ thống chấp nhận gửi lời mời, trả về thông báo “Invitation sent.”

> **Lưu ý**: Đây là lúc slot “chờ” chưa được tính hết, vì user chưa click link.

---

## 3. Đăng ký và xác minh email vượt slot

1. Mở email của **D** (thư mời vừa gửi), click link **Accept Invitation**.

2. Hoàn thành flow **Sign up & Verify email**.

3. Khi đăng nhập xong, bạn trở thành thành viên thứ **4**, dù giới hạn là 3.

   ```bash
   GET https://dust.tt/api/w/{workspaceSid}/members/{newUserId}
   → HTTP 200 OK
   {"role":"member",…}
   ```

4. Lặp lại với **E**, **F**,… đến khi mỏi tay — slot vẫn cho phép join bình thường.

---

## 4. Xác nhận vượt giới hạn

1. Truy cập lại **Workspace Settings → Members**, đếm số thành viên.
2. Bạn sẽ thấy **>3** user thực tế, vượt hẳn giới hạn gói Free.
3. Mời thêm (chưa accept) có thể lên tới hàng trăm rồi accept tất — chứng minh giới hạn bị phá.

---

## 5. Kịch bản thiệt hại

* **Lách gói Free**: Ai cũng có thể mời sẵn 100 email, rồi người mời click link, workspace sẽ có 100 member.
* **Mất doanh thu**: Khách hàng không cần nâng cấp gói Business vẫn có thể invite team size lớn.

---

