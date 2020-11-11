# Các kiểu dữ liệu trong Redis
- **Redis** hỗ trợ 5 kiểu dữ liệu
## **1) String**
- Có thể là **string**, **integer** hoặc **float**. **Redis** có thể làm việc với cả **string**, từng phần của **string**, cũng như tăng/giảm giá trị của **integer**, **float** .
- 1 string trong **redis** có độ dài tối đa là `512 MB` .
- Cú pháp :
    ```
    > COMMAND KEY_NAME
    ```
- **VD :**
    ```
    > SET name cuongnq
    OK
    > GET name
    cuongnq
    ```
    - `SET` và `GET` là các command, e
- Các command thường dùng :
    - `SET key_value` : dùng để set giá trị cho key được chỉ định
    - `GET key` : dùng để lấy giá trị đã được gán cho key
    - `GETRANGE key start end` : lấy ra chuỗi con của string được gắn cho key
    - `GETSET key value` : set giá trị mới cho key và trả về giá trị cũ
    - `GETBIT key offset` : trả về giá trị bit của string được gán cho key
    - `MGET key1 [key2..]` : lấy ra các giá trị được gán cho nhiều key
    - `SETBIT key offset value` : 
    - `SETEX key seconds value` : set giá trị cho key đi kèm thời gian hết hạn (exprire)
    - `SETNX key value` : set giá trị cho key, chỉ khi key không tồn tại
    - `SETRANGE key offset value` :
    - `STRLEN key` : trả về độ dài chuỗi string được gán cho key
    - `MSET key value [key value ...]` : set nhiều giá trị cho nhiều key
    - `MSETNX key value [key value ...]` : set nhiều giá trị cho nhiều key, chỉ khi các key không tồn tại
    - `PSETEX key milliseconds value` : set giá trị và thời gian hết hạn (expire) cho key
    - `INCR key` : tăng giá trị nguyên của key lên `1`
    - `INCRBY key increment` : tăng giá trị nguyên của key lên một khoảng cho trước
    - `INCRBYFLOAT key increment` : tăng giá trị thực của key lên một khoảng cho trước
    - `DECR key` : giảm giá trị nguyên của key xuống `1`
    - `DECRBY key decrement` : giảm giá trị nguyên của key xuống một khoảng cho trước
    - `APPEND key value` : thêm giá trị vào key
- [Tham khảo thêm](https://www.tutorialspoint.com/redis/redis_strings.htm)
## **2) Hashes**
- **Redis hashes** là dạng map giữa các field và các giá trị string. Do đó, chúng là kiểu dữ liệu hoàn hảo để biểu diễn các **object**.
- Trong **redis**, mỗi hash có thể lưu trữ hơn 4 tỉ cặp field-value .
- **VD :**
    ```
    > HMSET information name "cuongnq" age 16 country "VietNam" university "PTIT"
    OK
    > HGETALL information
    1) "name"
    2) "cuongnq"
    3) "age"
    4) "16"
    5) "country"
    6) "VietNam"
    7) "university"
    8) "PTIT"
    ```
    > Ở ví dụ này, ta set các chi tiết `name`, `age`, `country`, `university` trong hash `information`
- Các command thường dùng :
    - `HDEL key field2 [field2]` : xóa một hoặc nhiều field của hash
    - `HEXISTS key field` : check xem hash field có tồn tại không
    - `HGET key field` : trả về giá trị của hash field được lưu trong key
    - `HGETALL key` : trả về tất cả giá trị của các hash field được lưu trong key
    - `HINCRBY key field increment` : tăng các giá trị nguyên của các hash field lên 1 khoảng cho trước
    - `HINCRBYFLOAT key field increment` : tăng các giá trị thực của các hash field lên 1 khoảng cho trước
    - `HKEYS key` : trả về tất cả các field trong hash
    - `HLEN key` : trả về số các field trong hash
    - `HMGET key field1 [field2]` : trả về giá trị của các field được chỉ định trong hash
    - `HMSET key field1 value1 [field2 value2 ]` : set giá trị cho các field được chỉ định trong hash
    - `HSET key field value` : set giá trị string cho hash field
    - `HSETNX key field value` : set giá trị string cho hash field, chỉ khi field không tồn tại
    - `HVALS key` : trả về tất cả các giá trị của hash
    - `HSCAN key cursor [MATCH pattern] [COUNT count]` :
- [Tham khảo thêm](https://www.tutorialspoint.com/redis/redis_hashes.htm)
## **3) List**
- **List** đơn giản là một danh sách các string, được sắp xếp theo thứ tự được chèn vào. 
- Có thể thêm các phần tử vào đầu **list** hoặc cuối **list**
- Độ dài tối đa của list là <code>2<sup>32</sup>-1</code> phần tử (`~ 4.294.967.295`)
- **VD :**
    ```
    > LPUSH db redis
    (integer) 1
    > LPUSH db mongo
    (integer) 2
    > LPUSH db postgre
    (integer) 3
    > LRANGE db 0 10
    1) "postgre"
    2) "mongo"
    3) "redis"
    ```
- Các command thường dùng :
    - `BLPOP key1 [key2 ] timeout` : xoá và trả về phần tử đầu tiên trong list, hoặc block nó
    - `BRPOP key1 [key2 ] timeout` : xóa và trả về phần tử cuối cùng trong list, hoặc block nó
    - `BRPOPLPUSH source destination timeout` : xóa một giá trị trong list, đẩy nó qua list khác và trả về nó, hoặc block nó
    - `LINDEX key index` : trả về một phần tử trong list qua index của nó
    - `LINSERT key BEFORE|AFTER pivot value` : chèn phần tử vào trước hoặc sau một phần tử nào đó trong list
    - `LLEN key` : trả về độ dài của list
    - `LPOP key` : xóa và trả về phần tử đầu tiên của list
    - `LPUSH key value1 [value2]` : thêm một hoặc nhiều giá trị vào list
    - `LPUSHX key value` : thêm một hoặc nhiều giá trị vào list, chỉ khi list tồn tại
    - `LRANGE key start stop` : trả về khoảng giá trị trong list
    - `LREM key count value` : xóa nhiều phần tử trong list
    - `LSET key index value` : set giá trị cho một phần tử trong list qua index của nó
    - `LTRIM key start stop` : Cắt list tại các điểm được chỉ định
    - `RPOP key` : xóa và trả về phần tử cuối cùng trong list
    - `RPOPLPUSH source destination` : xóa giá trị cuối cùng trong list, đẩy nó qua list khác và trả về nó
    - `RPUSH key value1 [value2]` : thêm 1 hoặc nhiều giá trị vào list
    - `RPUSHX key value` : thêm một hoặc nhiều giá trị vào list, chỉ khi list tồn tại
- [Tham khảo thêm](https://www.tutorialspoint.com/redis/redis_lists.htm)
## **4) Set**
- [Tham khảo thêm](https://www.tutorialspoint.com/redis/redis_sets.htm)
## **5) Zset (Sorted Set)**
- [Tham khảo thêm](https://www.tutorialspoint.com/redis/redis_sorted_sets.htm)
