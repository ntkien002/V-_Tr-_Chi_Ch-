


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
Download not permission
```
### coppy link của file và Bypass bằng Curl thành công

```
 curl: http://abc.com/dowload/a.pdf 
```
