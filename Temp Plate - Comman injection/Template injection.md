

 Nếu attacker gửi `?name={{7*7}}`, server sẽ trả về `"Hello 49"` → **SSTI** (Server-Side Template Injection).

---

### 🕵️‍♂️ Cách phát hiện:

* Dò payload đơn giản:

  ```html
   {{7*7}} 
  ```
  
  ```
   #{7*7}
  ```
   ---
 ```
;id
```
```
$(id)
```
```
${@id}
```
```
%60id%60
```
* 
* Quan sát phản hồi từ server → nếu trả về kết quả tính toán => có khả năng injection.

---

