# Các thao tác với Database
### **TẠO DATABASE**
- **MongoDB** sử dụng lệnh `use DATABASE_NAME` để tạo database. Lệnh này sẽ tạo 1 database nếu nó chưa tồn tại, hoặc nó sẽ trả về 1 database đã tồn tại và sử dụng nó .
- Cú pháp :
    ```
    > use DATABASE_NAME
    ```
- **VD :**
    - Muốn tạo và sử dụng database `mydb` :
        ```
        > use mydb
        switched to db mydb
        ```
    - Để kiểm tra database đang sử dụng :
        ```
        > db
        mydb
        ```
    - Kiểm tra các database đang có :
        ```
        > show dbs
        admin   0.000GB
        config  0.000GB
        local   0.000GB
        ```
        > Database vừa tạo vẫn chưa được hiển thị, do chưa thực sự được tạo ra
    - Để thực sự tạo ra 1 database, cần insert tối thiểu một document vào nó :
        ```
        > db.movie.insert({"name":"avengers"})
        WriteResult({ "nInserted" : 1 })
        ```
        > Lúc này, đồng thời tạo ra 1 collection `movie` 
    - Kiểm tra lại các database đang có :
        ```
        > show dbs
        admin   0.000GB
        config  0.000GB
        local   0.000GB
        mydb    0.000GB
        ```
        > Database đã được tạo
- Trong MongoDB có một database mặc định là `test`. Nếu không khởi tạo database, collection sẽ được lưu trong database `test` .
### **XÓA DATABASE CÓ SẴN**
- Cú pháp :
    ```
    > db.dropDatabase()
    ```
- Lệnh này sẽ xóa database được chọn. Nếu chưa chọn database nào, database `test` sẽ bị xóa .
- **VD :** Xóa database `mydb` :
    ```
    > use mydb
    switched to db mydb
    > db.dropDatabase()
    { "dropped" : "mydb", "ok" : 1 }
    ```
### **COPY/CLONE DATABASE**


https://docs.mongodb.com/database-tools/mongodump/#mongodump-example-copy-clone-database