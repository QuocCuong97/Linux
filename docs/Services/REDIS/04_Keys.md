# Key trong Redis
- Key giống như tên đại diện cho 1 record trong **Redis**
- Cú pháp lệnh tương tác với key :
    ```
    > COMMAND KEY_NAME
    ```
- **VD :**
    ```
    > SET db redis
    OK
    > DEL db
    (integer) 1
    ```
    - Ở ví dụ này ta dùng lệnh `SET` để tạo 1 key là `db` có giá trị là `redis` sau đó xóa nó bằng lệnh `DEL`.  Kết quả lệnh `DEL` sẽ trả về là `(integer) 1` nếu xóa thành công hoặc trả về `(integer) 0` nếu xóa không thành công .
- Hiển thị tất cả các key đang có trong **redis** :
    ```
    > KEYS *
    ```
- Các command thường dùng :
    - `DEL key` : dùng để xóa key nếu key tồn tại
    - `DUMP key` : 
    - `EXISTS key` : kiểm tra xem key có tồn tại không
    - `EXPIRE key second` : set thời gian hết hạn (expiry) theo giây cho key
    - `EXPIREAT key timestamp` : set khoảng thời gian (timestamp - Unix format) mà key hết hạn. 
    - `PEXPIRE key milliseconds` : set thời gian hết hạn (expiry) cho key theo miligiây
    - `PEXPIREAT key milliseconds-timestamp` : set thời gian hết hạn (expiry) cho key theo miligiây timestamp
    - `KEYS pattern` :
    - `MOVE key db` : chuyển key đến database khác
    - `PERSIST key` : xóa phần hết hạn cho key
    - `PTTL key` : lấy thời gian còn lại trước khi key hết hạn theo miligiây
    - `TTL key` : lấy thời gian còn lại trước khi key hết hạn
    - `RANDOMKEY` : 
    - `RENAME key newkey` : thay đổi key name
    - `RENAMENX key newkey` : thay đổi key name, nếu key name không tồn tại
    - `TYPE key` : trả về kiểu dữ liệu được lưu trong key