# https://hackerone.com/reports/2380133


**Loại lỗi:** Broken / Improper Access Control
**Mức độ:** Low (3.2)
**Phạm vi:** Mobile app (Android) – không xảy ra trên Web UI

---

## Tóm tắt ngắn gọn

Chủ file **tắt quyền Download** khi share folder, nhưng **user nhận share vẫn tải được file qua Android app** bằng các chức năng export / use-as trong viewer.
Backend / mobile client **không enforce đúng quyền “Allow download”** cho một số định dạng file.

---

## Bối cảnh

* User1: owner file
* User2: người được share (download bị disable)

---

## 1️⃣ Request đúng → Response đúng (hành vi mong đợi)

### Điều kiện

* File được share với **Allow download = OFF**

### Hành vi đúng

* User2 **chỉ xem (preview)** file
* Không có cách nào lưu file ra thiết bị

### Response đúng (khi export / download)

```
403 Forbidden
{
  "error": "Download not allowed"
}
```

👉 Quyền download được enforce đúng

---

## 2️⃣ Request bug → Response bug (hành vi sai)

### Hành vi trên Android app

User2 mở file trong viewer và chọn các option sau:

#### 📄 PDF / ODT / ODP

* `Download as PDF`
* `Download as EPUB`
* `Save as`

➡️ App hiển thị:

```
Download completed
```

➡️ File được lưu vào thiết bị ❌

---

#### 🖼️ Image (.png / .jpg)

* `Use image as`

  * Wallpaper
  * Avatar
  * Contact photo

➡️ File được ghi vào **internal storage** ❌

---

### Response bug (logic)

* Không có lỗi
* Không có deny từ backend
* App xử lý export như **download hợp lệ**

---

## Các định dạng bị ảnh hưởng

✅ Bị leak:

* `.pdf`
* `.odt`
* `.odp`
* `.png`, `.jpg`

⚠️ Không tải được / lỗi:

* `.mp3`, `.mp4`, `.txt` → xem được, không tải
* `.md`, `.csv` → viewer load lỗi
* `File > Save as` → infinite loading (UI bug)

---

## Bản chất lỗi

* Quyền **Allow download**:

  * Enforced đúng ở Web
  * ❌ Không enforced đầy đủ ở Android viewer
* Viewer cho phép:

  * Export
  * Convert
  * Use-as
    → **Bypass logic “no-download”**

---

## Impact

* Leak tài liệu nhạy cảm
* Bypass quyền chia sẻ
* Đặc biệt nguy hiểm với:

  * PDF hợp đồng
  * Tài liệu nội bộ
  * Hình ảnh riêng tư

---
