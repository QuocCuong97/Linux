# Projection
- Trong **MongoDB**, **projection** có nghĩa chỉ chọn nhưng trường data cần thiết để show thay vì show toàn bộ các trường của document. Nếu document có 5 trường nhưng chỉ muốn show 3, chỉ cần chọn 3 trường đó thôi.
#### **Phương thức `find()`**
- Phương thức `find()` trong **MongoDB** sẽ chấp nhận tham số thứ 2 là list các trường muốn nhận được trong truy vấn.
- Mặc định, khi thực hiện lệnh `find()`, **MongoDB** sẽ trả về tất cả các field của document. Để giới hạn, cần set danh sách các field trả về với các giá trị là `0` hoặc `1`. `1` là để hiển thị field còn `0` là để ẩn field.
- Cú pháp :
    ```
    > db.COLLECTION_NAME.find({},{KEY:1})
    ```
- **VD :**
    - Một collection có sẵn :
        ```
        { "_id" : ObjectId("5f801e753f4a3bdc6655972c"), "name" : "Captain America", "director" : "Russos", "year" : "2011" }
        { "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor", "director" : "Russos", "year" : "2017" }
        { "_id" : ObjectId("5f8021403f4a3bdc66559732"), "name" : "IronMan", "director" : "Russos", "year" : "2010" }
        ```
    - Truy vấn chỉ hiển thị field `name` và `year` của collection :
        ```
        > db.movie.find({}, {"name": 1, "year": 1})
        { "_id" : ObjectId("5f801e753f4a3bdc6655972c"), "name" : "Captain America", "year" : "2011" }
        { "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor", "year" : "2017" }
        { "_id" : ObjectId("5f8021403f4a3bdc66559732"), "name" : "IronMan", "year" : "2010" }
        ```
        > Trường `_id` sẽ luôn hiển thị khi thực hiện truy vấn, nếu không muốn hiển thị, cần set `_id` là `0` :
        ```
        > db.movie.find({}, {_id: 0, "name": 1, "year": 1})
        { "name" : "Captain America", "year" : "2011" }
        { "name" : "Thor", "year" : "2017" }
        { "name" : "IronMan", "year" : "2010" }
        ```
