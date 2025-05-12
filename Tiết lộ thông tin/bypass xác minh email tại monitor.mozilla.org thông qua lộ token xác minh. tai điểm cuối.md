# https://hackerone.com/reports/2712583
---

### 💥 **Lỗi chính**: *Bypass xác minh email tại `monitor.mozilla.org` thông qua lộ token xác minh.*

#### ⚔️ Mô tả tấn công:

Kẻ tấn công có thể:

1. **Thêm email tùy ý** vào hệ thống theo dõi của Mozilla Firefox Monitor — yêu cầu xác minh email.
2. **Truy cập endpoint `/api/v1/user/breaches`** — nơi *lộ* cả thông tin các email chưa xác minh cùng với **token xác minh**.
3. Sau đó chỉ cần gửi GET request đến:

```
/api/v1/user/verify-email?token=<LEAKED_TOKEN>
```

\=> **Xác minh email mà không cần truy cập hòm thư!**

