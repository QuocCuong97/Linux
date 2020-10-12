# Sort Record
#### **Phương thức `sort()`**
- Để sort các document trong **MongoDB**, cần sử dụng phương thức `sort()`. Phương thức chấp nhận 1 document chứa 1 list các trường đi kèm với thứ tự sắp xếp .
- Sử dụng `1` và `-1` để chỉ định chiều sắp xếp :
    - `1` để sắp xếp tăng dần
    - `-1` để sắp xếp giảm dần
- Cú pháp :
    ```
    > db.COLLECTION_NAME.find().sort({KEY:1})
    ```
- **VD :** Sắp xếp giảm dần theo field `name` :
    ```
    > db.movie.find().sort({name:-1})
    { "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor", "director" : "Russos", "year" : "2017" }
    { "_id" : ObjectId("5f8021403f4a3bdc66559732"), "name" : "IronMan", "director" : "Russos", "year" : "2010" }
    { "_id" : ObjectId("5f8021403f4a3bdc66559730"), "name" : "Captain America", "director" : "Russos", "year" : "2011" }
    ```