# Các thao tác với Document
### **KHÁI NIỆM OBJECT ID**
- Một **ObjectID** là một chuỗi **BSON** dài `12-byte` có cấu trúc sau :
    - `4-bytes` đầy đại diện cho timestamp thời điểm document được tạo ra
    - `3-bytes` tiếp theo là machine identifier
    - `2-bytes` tiếp theo chứ process-id
    - `3-bytes` cuối là giá trị random
- **MongoDB** sử dụng **ObjectID** như giá trị mặc định của trường `_id` của mỗi document, được tự động generate mỗi khi tạo một document . Cấu trúc phức tạp của **ObjectID** tạo cho các giá trị trong trường `_id` sự duy nhất .
- Có thể tạo một **objectID** bằng cú pháp sau :
    ```
    > newObjectId = ObjectId()
    ObjectId("5f801b68feb37783ef223282")
    ```
- Lấy timestamp thời điểm tạo document qua **ObjectID** :
    ```
    > ObjectId("5f801b68feb37783ef223282").getTimestamp()
    ISODate("2020-10-09T08:12:24Z")
    ```
    > Đây là time **UTC**
### **INSERT DOCUMENT**
- Cú pháp :
    ```
    > db.COLLECTION_NAME.insert(document)
    ```
- **VD1 :** Insert 1 document :
    ```
    > db.movie.insert({
    name: "Avengers",
    director: "Russos",
    year: "2019",
    })
    WriteResult({ "nInserted" : 1 })
    ```
- **VD2 :** Insert một mảng nhiều document :
    ```
    > db.movie.insert([{
    name: "Captain America",
    director: "Russos",
    year: "2011",
    },{
    name: "Thor",
    director: "Russos",
    year: "2013",
    },{
    name: "IronMan",
    director: "Russos",
    year: "2008",
    }])
    BulkWriteResult({
            "writeErrors" : [ ],
            "writeConcernErrors" : [ ],
            "nInserted" : 3,
            "nUpserted" : 0,
            "nMatched" : 0,
            "nModified" : 0,
            "nRemoved" : 0,
            "upserted" : [ ]
    })
    ```
#### **Phương thức `insertOne()`**
- Nếu chỉ muốn insert 1 document vào collection có thể sử dụng phương thức này . Phương thức này sẽ trả về **ObjectID** của document được insert .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.insertOne(document)
    ```
- **VD :**
    ```
    > db.movie.insertOne({
    name: "Avengers",
    director: "Russos",
    year: "2019",
    })
    {
            "acknowledged" : true,
            "insertedId" : ObjectId("5f801fb93f4a3bdc6655972f")
    }
    ```
#### **Phương thức `insertMany()`**
- Có thể insert nhiều document sử dụng phương thức `insertMany()`. Cần truyền vào một mảng các document . Phương thức này sẽ trả về một mảng các **ObjectID** của các document được insert .
- **VD :**
    ```
    > db.movie.insertMany([{
    name: "Captain America",
    director: "Russos",
    year: "2011",
    },{
    name: "Thor",
    director: "Russos",
    year: "2013",
    },{
    name: "IronMan",
    director: "Russos",
    year: "2008",
    }])
    {
            "acknowledged" : true,
            "insertedIds" : [
                    ObjectId("5f8021403f4a3bdc66559730"),
                    ObjectId("5f8021403f4a3bdc66559731"),
                    ObjectId("5f8021403f4a3bdc66559732")
            ]
    }
    ```
### **QUERY DOCUMENT**
#### **Phương thức `find()`**
- Để query dữ liệu từ **collection** sử dụng phương thức `find()` .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.find()
    ```
    > Phương thức `find()` sẽ trả lại các document không theo cấu trúc cố định
- **VD1 :**
    ```
    > db.movie.find()
    { "_id" : ObjectId("5f801e753f4a3bdc6655972c"), "name" : "Captain America", "director" : "Russos", "year" : "2011" }
    { "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor", "director" : "Russos", "year" : "2013" }
    { "_id" : ObjectId("5f801e753f4a3bdc6655972e"), "name" : "IronMan", "director" : "Russos", "year" : "2008" }
    { "_id" : ObjectId("5f801fb93f4a3bdc6655972f"), "name" : "Avengers", "director" : "Russos", "year" : "2019" }
    ```
#### **Phương thức `pretty()`**
- Để hiển thi kết quả dưới dạng cấu trúc đẹp hơn, sử dụng phương thức `pretty()` .
- **VD :**
    ```
    > db.movie.find().pretty()
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972c"),
            "name" : "Captain America",
            "director" : "Russos",
            "year" : "2011"
    }
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972d"),
            "name" : "Thor",
            "director" : "Russos",
            "year" : "2013"
    }
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972e"),
            "name" : "IronMan",
            "director" : "Russos",
            "year" : "2008"
    }
    {
            "_id" : ObjectId("5f801fb93f4a3bdc6655972f"),
            "name" : "Avengers",
            "director" : "Russos",
            "year" : "2019"
    }
    ```
#### **Phương thức `findOne()`**
- Phương thức này sẽ chỉ trả về kết quả đầu tiên của truy vấn, nhưng ở dạng pretty .
- Cú pháp :
    ```
    > db.COLLECTIONNAME.findOne()
    ```
- **VD :**
    ```
    > db.movie.findOne()
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972c"),
            "name" : "Captain America",
            "director" : "Russos",
            "year" : "2011"
    }
    ```
### **MỆNH ĐỀ TRUY VẤN TRONG MONGODB**
#### **Các cấu trúc so sánh**
- Để query document dựa theo điều kiện, sử dụng các biểu thức query sau :

    | Operation | Cú pháp | Ví dụ | RDBMS |
    |-----------|--------|---------|-------|
    | Equality | `{<key>:{$eg;<value>}}` | `db.mycol.find({"by":"tutorials point"}).pretty()` | `where by = 'tutorials point'` |
    | Less Than | `{<key>:{$lt:<value>}}` | `db.mycol.find({"likes":{$lt:50}}).pretty()` | `where likes < 50` |
    | Less Than Equals | `{<key>:{$lte:<value>}}` | `db.mycol.find({"likes":{$lte:50}}).pretty()` | `where likes <= 50` |
    | Greater Than | `{<key>:{$gt:<value>}}` | `db.mycol.find({"likes":{$gt:50}}).pretty()` | `where likes > 50` |
    | Greater Than Equals | `{<key>:{$gte:<value>}}` | `db.mycol.find({"likes":{$gte:50}}).pretty()` | `where likes >= 50` | 
    | Not Equals | `{<key>:{$ne:<value>}}` | `db.mycol.find({"likes":{$ne:50}}).pretty()` | `where likes != 50` | 
    | Values in an array | `{<key>:{$in:[<value1>, <value2>,……<valueN>]}}` | `db.mycol.find({"name":{$in:["Raj", "Ram", "Raghu"]}}).pretty()` | `Where name matches any of the value in :["Raj", "Ram", "Raghu"]` |
    | Values not in an array | `{<key>:{$nin:<value>}}` | `db.mycol.find({"name":{$nin:["Ramu", "Raghav"]}}).pretty()` | `Where name values is not in the array :["Ramu", "Raghav"] or, doesn’t exist at all` |
#### **`AND` trong MongoDB**
- Để truy vấn document dựa trên điều kiện `AND`, cần sử dụng từ khóa `$and` theo cấu trúc sau :
    ```
    > db.COLLECTION_NAME.find({ $and: [ {<key1>:<value1>}, { <key2>:<value2>} ] })
    ```
- **VD :**
    ```
    > db.movie.find({$and:[{director: "Russos"}, {year: "2008"}]}).pretty()
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972e"),
            "name" : "IronMan",
            "director" : "Russos",
            "year" : "2008"
    }
    ```
#### **`OR` trong MongoDB**
- Để truy vấn document dựa trên điều kiện `OR`, cần sử dụng từ khóa `$or` theo cấu trúc sau :
    ```
    > db.COLLECTION_NAME.find({$or: [ {<key1>: <value1>}, {<key2>: <value2>} ] })
    ```
- **VD :**
    ```
    > db.movie.find({$or:[{year: "2019"}, {year: "2008"}]}).pretty()
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972e"),
            "name" : "IronMan",
            "director" : "Russos",
            "year" : "2008"
    }
    {
            "_id" : ObjectId("5f801fb93f4a3bdc6655972f"),
            "name" : "Avengers",
            "director" : "Russos",
            "year" : "2019"
    }
    ```
#### **`AND` và `OR` kết hợp**
- **VD :**
    ```
    > db.movie.find({director: "Russos", $or:[{year: "2019"}, {year: "2008"}]}).pretty()
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972e"),
            "name" : "IronMan",
            "director" : "Russos",
            "year" : "2008"
    }
    {
            "_id" : ObjectId("5f801fb93f4a3bdc6655972f"),
            "name" : "Avengers",
            "director" : "Russos",
            "year" : "2019"
    }
    ```
#### **`NOR` trong MongoDB**
- Để truy vấn document dựa trên điều kiện `NOR` (`NOT (...OR...)`), cần sử dụng từ khóa `$nor` theo cấu trúc sau :
- Cú pháp :
    ```
    > db.COLLECTION_NAME.find({$nor: [ {<key1>: <value1>}, {<key2>: <value2>} ] })
    ```
- **VD :**
    ```
    > db.movie.find({$nor: [{year: "2019"}, {year: "2008"}]}).pretty()
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972c"),
            "name" : "Captain America",
            "director" : "Russos",
            "year" : "2011"
    }
    {
            "_id" : ObjectId("5f801e753f4a3bdc6655972d"),
            "name" : "Thor",
            "director" : "Russos",
            "year" : "2013"
    }
    ```
#### **`NOT` trong MongoDB**
- Để truy vấn document dựa trên điều kiện `NOT` , cần sử dụng từ khóa `$not` theo cấu trúc sau :
- Cú pháp :
    ```
    > db.COLLECTION_NAME.find({$not: {<key>: <value>} })
    ```
- **VD :**
    ```
    > db.movie.find({"year": {$not: {$gt: "2016"}}})
    { "_id" : ObjectId("5f801e753f4a3bdc6655972c"), "name" : "Captain America", "director" : "Russos", "year" : "2011" }
    { "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor", "director" : "Russos", "year" : "2013" }
    { "_id" : ObjectId("5f801e753f4a3bdc6655972e"), "name" : "IronMan", "director" : "Russos", "year" : "2008" }
    ```
### **UPDATE DOCUMENT**
#### **Phương thức `update()`**
- Phương thức `update()` dùng để update giá trị trong document có sẵn
- Cú pháp :
    ```
    > db.COLLECTION_NAME.update(SELECTION_CRITERIA, UPDATED_DATA)
    ```
- **VD :** Update document `{ "_id" : ObjectId("5f801e753f4a3bdc6655972d"), "name" : "Thor", "director" : "Russos", "year" : "2013" }` : 
    ```
    > db.movie.update({'year': '2013'}, {$set: {'year': '2017'}})
    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
    ```
- Mặc định, phương thức `update()` sẽ chỉ update document đầu tiên match với điều kiện. Muốn update tất cả các document match với điều kiện, sử dụng tham số `{multi:true}` :
    ```
    > db.movie.update({'year': '2013'}, {$set: {'year': '2017'}}, {multi:true})
    WriteResult({ "nMatched" : 2, "nUpserted" : 0, "nModified" : 2 })
    ```
#### **Phương thức `save()`**
- Phương thức `save()` thay thế một document đang có bằng một document được đưa vào trong phương thức.
- Cú pháp :
    ```
    > db.COLLECTION_NAME.save({_id:ObjectId(),NEW_DATA})
    ```
- **VD :**
    ```
    > db.movie.save({_id: ObjectId("5f801fb93f4a3bdc6655972f"), "name" : "The Avengers", "director" : "Russos", "year" : "2012"})
    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
    ```
#### **Phương thức `findOneAndUpdate()`**
- Phương thức `findOneAndUpdate()` dùng để update giá trị trong document có sẵn .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.findOneAndUpdate(SELECTIOIN_CRITERIA, UPDATED_DATA)
    ```
- **VD :**
    ```
    > db.movie.findOneAndUpdate({"name": "The Avengers"}, {$set: {"year": "2015"}})
    {
            "_id" : ObjectId("5f801fb93f4a3bdc6655972f"),
            "name" : "The Avengers",
            "director" : "Russos",
            "year" : "2012"
    }
    ```
#### **Phương thức `updateOne()`**
- Phương thức `updateOne()` update một document match với filter cho trước .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.updateOne(<filter>, <update>)
    ```
- **VD :**
    ```
    > db.movie.updateOne({"name": "The Avengers"}, {$set: {"year": "2012"}})
    { "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
    ```
#### **Phương thức `updateMany()`**
- Phương thức `updateMany()` update một document match với filter cho trước .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.updateMany(<filter>, <update>)
    ```
- **VD :**
    ```
    > db.movie.updateMany({"name": "IronMan"}, {$set: {"year": "2010"}})
    { "acknowledged" : true, "matchedCount" : 2, "modifiedCount" : 2 }
    ```
### **DELETE DOCUMENT**
#### **Phương thức `remove()`**
- Phương thức `remove()` được sử dụng để xóa document khỏi collection.
- Phương thức `remove()` chấp nhận 2 tham số :
    - `DELETION_CRITERIA` (tùy chọn) : điều kiện lọc để document bị xóa
    - `justOne` (tùy chọn) : nếu set là `true` hoặc `1`, sẽ chỉ 1 document bị xóa
- Cú pháp :
    ```
    > db.COLLECTION_NAME.remove(DELLETION_CRITTERIA)
    ```
- **VD1 :** Xóa tất cả các document :
    ```
    > db.movie.remove({"name": "Captain America"})
    WriteResult({ "nRemoved" : 2 })
    ```
- **VD2 :** Xóa document đầu tiên tìm được :
    ```
    > db.movie.remove({"name": "IronMan"},1)
    WriteResult({ "nRemoved" : 1 })
