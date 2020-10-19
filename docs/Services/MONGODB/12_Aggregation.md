# Aggregation
- Các hoạt động **aggregation** xử lý các dữ liệu từ documet và trả về kết quả sau khi đã tính toán. **Aggregation** sẽ nhóm giá trị của nhiều document lại với nhau, và có thể thực hiện nhiều thao tác trên dữ liệu đã nhóm để trả về 1 kết quả. Hoạt động này tương đương với `count(*)` và `with group by` trong **SQL** .
#### **Phương thức `aggregate()`**
- Sử dụng phương thức `aggregate()` để thực hiện aggregation trong **MongoDB**
- Cú pháp :
    ```
    > db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)
    ```
- **VD :**
    - Có sẵn collection sau :
        ```
        { "_id" : ObjectId("5f801e753f4a3bdc6655972c"), "name" : "Captain America", "director" : "Russos", "year" : "2011" }
        { "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor 3", "director" : "Russos", "year" : "2017" }
        { "_id" : ObjectId("5f8021403f4a3bdc66559730"), "name" : "Thor 1", "director" : "Russos", "year" : "2011" }
        { "_id" : ObjectId("5f8021403f4a3bdc66559731"), "name" : "Captain America 3", "director" : "Russos", "year" : "2017" }
        { "_id" : ObjectId("5f8021403f4a3bdc66559732"), "name" : "IronMan", "director" : "Russos", "year" : "2010" }
        ```
    - Đếm những `movie` có cùng `year` :
        ```
        > db.movie.aggregate([{$group : {_id : "$year", total : {$sum : 1}}}])
        { "_id" : "2010", "total" : 1 }
        { "_id" : "2017", "total" : 2 }
        { "_id" : "2011", "total" : 2 }
        ```
        > Câu lệnh tương đương với `select year, count(*) from movie group by year`.
- Một số các hàm tính toán :

    | Hàm | Mô tả | Ví dụ |
    |-----|-------|-------|
    | `$sum` | Cộng tất cả các giá trị được chỉ định từ các document trong collection | `db.movie.aggregate([{$group : {_id : "$year", total : {$sum : "$name"}}}])` |
    | `$avg` | Tính toán giá trị trung bình của các giá trị được chỉ định từ các document trong collection | `db.movie.aggregate([{$group : {_id : "$by_user", total : {$avg : "$likes"}}}])` |
    | `$min` | Tính toán giá trị nhỏ nhất của các giá trị được chỉ định từ các document trong collection | `db.movie.aggregate([{$group : {_id : "$by_user", total : {$min : "$likes"}}}])` |
    | `$max` | Tính toán giá trị lớn nhất của các giá trị được chỉ định từ các document trong collection | `db.movie.aggregate([{$group : {_id : "$by_user", total : {$max : "$likes"}}}])` |
    | `$push` | Chèn giá trị vào 1 mảng trong document trả ra | `db.movie.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}])` |
    | `$addToSet` | Chèn giá trị vào 1 mảng trong document trả ra nhưng không tạo trùng lặp | `db.movie.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}])` |
    | `$first` | Gets the first document from the source documents according to the grouping. Typically this makes only sense together with some previously applied “$sort”-stage. | `db.movie.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}])` |
    | `$last` | Gets the last document from the source documents according to the grouping. Typically this makes only sense together with some previously applied “$sort”-stage. | `db.movie.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}])` |
### **Pipeline Concept**
- Trong **UNIX**, pipeline có nghĩa là khả năng thực hiện một hành động dựa trên input và trả ra output được coi như input của lệnh sau,... . **MongoDB** cũng hỗ trợ concept tương tự khi sử dụng **aggregation**
