# https://hackerone.com/reports/1639011
---

 **API keys nhạy cảm của Planet Labs bị lộ công khai qua snapshot của Wayback Machine**, một lỗi sơ đẳng nhưng cực kỳ nguy hiểm trong thực tế.

---

### 🎯 **Tóm tắt lỗi**

* Các URL endpoint của API `https://api.planet.com/...` bị thu thập snapshot trên [web.archive.org](https://web.archive.org).
* Trong nội dung snapshot, **API keys hợp lệ** vẫn còn hiển thị rõ ràng.
* Kết quả: bất kỳ ai **truy cập Wayback Machine** đều có thể **thu thập và tái sử dụng các khóa API** để gọi các dịch vụ mà không cần xác thực bổ sung.

---

### 🧪 **POC (Proof of Concept)**

1. **Truy cập Wayback Machine**:

   ```
   https://web.archive.org/web/*/https://api.planet.com/*
   ```

2. **Tìm snapshot chứa key thật** (ví dụ):

   ```bash
   https://api.planet.com/basemaps/v1/mosaics?api_key=afdb1e8a9c8142739553e3942283d6c8&_page_size=1000
   https://api.planet.com/basemaps/v1/mosaics/wmts?service=wmts&request=GetCapabilities&format=text%2Fxml&api_key=8fe044edc78c46ba904bb62e550493a3
   ```

3. **Gửi request** qua curl hoặc Burp:

   ```bash
   curl "https://api.planet.com/basemaps/v1/mosaics?api_key=afdb1e8a9c8142739553e3942283d6c8"
   ```

4. **API phản hồi dữ liệu**? => **Key còn sống, có thể khai thác**.

---

### 🚨 **Tác động thực tế**

* **Truy cập trái phép tài nguyên bản đồ vệ tinh**.
* **Tiêu tốn quota API của chủ sở hữu**.
* **Có thể dẫn đến các hành vi thu thập, mapping, hoặc pivot khai thác khác**.
* **Rò rỉ hệ thống định danh/địa lý nhạy cảm** trong môi trường quốc phòng, GIS...

---

