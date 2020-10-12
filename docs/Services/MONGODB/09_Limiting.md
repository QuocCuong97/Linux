# Limit Record
#### **Phương thức `limit()`**
- Để giới hạn các record trả về trong **MongoDB**, sử dụng phương thức `limit()`. Phương thức chấp nhận 1 tham số, chính là số document muốn hiển thị sau truy vấn .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.find().limit(NUMBER)
    ```
- **VD :**
    ```
    > db.movie.find().limit(2)
    { "_id" : ObjectId("5f801e753f4a3bdc6655972c"), "name" : "Captain America", "director" : "Russos", "year" : "2011" }
    { "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor", "director" : "Russos", "year" : "2017" }
    ```
#### **Phương thức `skip()`**
- Ngoài phương thức `limit()`, còn một phương thức `skip()` cũng chấp nhận tham số là 1 số sử dụng để nhảy qua các kết quả trả về .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
    ```
- **VD :**
    ```
    > db.movie.find()
    { "_id" : ObjectId("5f801e753f4a3bdc6655972c"), "name" : "Captain America", "director" : "Russos", "year" : "2011" }
    { "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor", "director" : "Russos", "year" : "2017" }
    { "_id" : ObjectId("5f8021403f4a3bdc66559730"), "name" : "Captain America", "director" : "Russos", "year" : "2011" }
    { "_id" : ObjectId("5f8021403f4a3bdc66559731"), "name" : "Thor", "director" : "Russos", "year" : "2017" }
    { "_id" : ObjectId("5f8021403f4a3bdc66559732"), "name" : "IronMan", "director" : "Russos", "year" : "2010" }
    ```
    ```
    > db.movie.find().limit(2).skip(3)
    { "_id" : ObjectId("5f8021403f4a3bdc66559731"), "name" : "Thor", "director" : "Russos", "year" : "2017" }
    { "_id" : ObjectId("5f8021403f4a3bdc66559732"), "name" : "IronMan", "director" : "Russos", "year" : "2010" }
    ```
    > Phương thức `skip()` sẽ bỏ qua 3 document sau truy vấn, sau đó trả về limit 2 document tiếp theo
