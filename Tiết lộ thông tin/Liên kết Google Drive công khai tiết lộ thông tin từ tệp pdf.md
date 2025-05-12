# https://hackerone.com/reports/2926447
---

## 🎯 **Mục tiêu**

Tìm thấy một **liên kết Google Drive công khai** chứa tài liệu quân sự có chứa **PII** (Họ tên, SSN, địa chỉ, mức độ bảo mật...) – thuộc phạm vi nhạy cảm của DoD.

---

## 🧨 **Chi tiết khai thác (POC)**

### Các bước tái tạo:

1. **Truy cập** trang web công khai tại:

   ```
   https://██████.aspx?Mode=ReadOnly&Id=90dd0d3b-0ed1-e76b-128f-11ebc799ba55
   ```
2. **Cuộn xuống cuối trang**, phát hiện liên kết dẫn đến:

   ```
   https://drive.google.com/drive/folders/████████
   ```
3. **Truy cập thư mục Google Drive**.
4. Mở thư mục có tên `██████ Internal`.
5. Điều hướng đến thư mục `Orders`.
6. Trong thư mục chứa nhiều file `.pdf` – rò rỉ:

   * Họ tên đầy đủ
   * Số An sinh xã hội (SSN)
   * Địa chỉ nhà
   * Mức độ bảo mật
   * Tình trạng hôn nhân & người phụ thuộc

📌 **Lưu ý:** Nội dung đã được cung cấp bằng ảnh chụp màn hình minh chứng (sensitive redacted).

---

## ⚠️ **Tác động**

* **Rò rỉ PII** – vi phạm Đạo luật Bảo mật Hoa Kỳ 1974.
* **Nguy cơ trộm cắp danh tính**, gian lận tài chính, lừa đảo.
* Ảnh hưởng tới nhân sự quân đội và các hoạt động nhạy cảm.

---
