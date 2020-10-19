# Index trong Mongo
- **Index** hỗ trợ một cách hiệu quả cho các truy vấn. Không có **index**, **MongoDB** sẽ scan tất cả các document của collection để chọn các document match với điều kiện query. Quá trình truy vấn này rất kém hiệu quả và yêu cầu **MongoDB** phải xử lý một lượng lớn dữ liệu ,
- **Index** là một cấu trúc dữ liệu đặc biệt, lưu trữ 1 phần nhỏ của data set trong một dạng preview. **Index** sẽ lưu trữ giá trị của 1 field được chỉ định trong set các field, sắp xếp giá trị của field theo thứ tự chỉ định trước .
- Các loại index trong **MongoDB** :
    - Single Field Indexes
    - Compound Indexes
#### **Phương thức `createIndex()`**
- Để tạo một **index**, cần sử dụng phương thức `createIndex()` .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.createIndex({KEY:1})
    ```
    > Ở đây `KEY` là tên của field muốn tạo **index** và `1` là sắp xếp theo thứ tự tăng dần. Để tạo **index** theo thứ tự giảm dần sử dụng `-1` .
- Phương thức `creatIndex()` có thể truyền vào nhiều field để tạo **index** trên nhiều field :  
    ```
    > db.COLLECTION_NAME.createIndex({KEY_1:1, KEY_2:-1,...})
    ```
- Các option có thể đi kèm trong phương thức :
    - `background` (Boolean): tạo **index** trong tiến trình nền vì vậy không làm ảnh hưởng đến các hoạt động của database. Chỉ định là `true` nếu muốn tạo trong tiến trình nền. Mặc định là `false` .
    - `unique` (Boolean): tạo **index** riêng biệt để collection sẽ không chấp nhận việc chèn document có index key hoặc key trùng với giá trị có trong **index**. Chỉ định là `true` nếu muốn tạo **index** riêng biệt. Mặc định là `false` .
    - `name` (string) : tên của **index**. Nếu không chỉ định, **MongoDB** tự động gen tên của **index** bằng cách kết hợp tên của trường index và thứ tự sắp xếp .
    - `sparse` (Boolean) : nếu set là `true`
    - `expireAfterSeconds` (integer) :
    - `weights`
    - `default_language` :
    - `language_override` :
- **VD :**
    ```
    > db.movie.createIndex({"name":1})
    {
            "createdCollectionAutomatically" : false,
            "numIndexesBefore" : 1,
            "numIndexesAfter" : 2,
            "ok" : 1
    }
    ```
#### **Phương thức `dropIndex()`**
- Có thể bỏ một index bằng các sử dụng phương thức `dropIndex()` trong **MongoDB**
- Cú pháp :
    ```
    > db.COLLECTION_NAME.dropIndex({KEY:1})
    ```
- **VD :**
    ```
    > db.movie.dropIndex({"name":1})
    { "nIndexesWas" : 2, "ok" : 1 }
    ```
#### **Phương thức `dropIndexes()`**
- Phương thức này sẽ xóa nhiều index trong collection .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.dropIndexes()
    ```
- **VD :**
    ```
    > db.movie.dropIndexes({"name":1,"year":-1})
    { "nIndexesWas" : 2, "ok" : 1 }
    ```
#### **Phương thức `getIndexes()`**
- Phương thức này trả về mô tả của tất cả các index trong collection .
- Cú pháp :
    ```
    > db.COLLECTION_NAME.getIndexes()
    ```
- **VD :** 
    - Trước hết tạo 2 index :
        ```
        > db.movie.createIndex({"name":1,"year":-1})
        ```
    - Hiển thị tất cả index của collection `movie` :
        ```
        > db.movie.getIndexes()
        [
                {
                        "v" : 2,
                        "key" : {
                                "_id" : 1
                        },
                        "name" : "_id_"
                },
                {
                        "v" : 2,
                        "key" : {
                                "name" : 1,
                                "year" : -1
                        },
                        "name" : "name_1_year_-1"
                }
        ]
        ```
#### **Hiệu năng khi sử dụng Index**
- **VD :** Trong database `mydb` có một collection `users` với `1.000.000` document . Mặc định, trường `_id` được sinh ra sẽ tự động là 1 index :
    ```
    > db.users.getIndexes()
    [ { "v" : 2, "key" : { "_id" : 1 }, "name" : "_id_" } ]
    ```
    - Đo tốc độ query 1 document trong collection bằng hàm `explain()`:
        ```
        > db.users.find({"username": "user1002"}).explain("executionStats")["executionStats"]
        {
            "executionSuccess" : true,
            "nReturned" : 1,
            "executionTimeMillis" : 639,
            "totalKeysExamined" : 0,
            "totalDocsExamined" : 1000000,
            "executionStages" : {
                "stage" : "COLLSCAN",
                "filter" : {
                    "username" : {
                        "$eq" : "user1002"
                    }
                },
                "nReturned" : 1,
                "executionTimeMillisEstimate" : 55,
                "works" : 1000002,
                "advanced" : 1,
                "needTime" : 1000000,
                "needYield" : 0,
                "saveState" : 1000,
                "restoreState" : 1000,
                "isEOF" : 1,
                "direction" : "forward",
                "docsExamined" : 1000000
            }
        }
        ```
        - Kết quả cho thấy :
            - `"totalKeysExamined" : 0` : query không sử dụng index Key
            - `"totalDocsExamined" : 1000000` : query thực hiện  quét toàn bộ `1.000.000` document
            - `"executionTimeMillisEstimate" : 55` : thời gian query là `55ms`
    - Do nhu cầu cần query field `username`, vì vậy cần tạo index cho trường `username` :
        ```
        > db.users.createIndex({username: 1}, {background: true,name: 'username_index'})
        {
            "createdCollectionAutomatically" : false,
            "numIndexesBefore" : 1,
            "numIndexesAfter" : 2,
            "ok" : 1
        }
        ```
    - Thử thực hiện lại query trên :
        ```
        > db.users.find({"username": "user1002"}).explain("executionStats")["executionStats"]
        {
            "executionSuccess" : true,
            "nReturned" : 1,
            "executionTimeMillis" : 2,
            "totalKeysExamined" : 1,
            "totalDocsExamined" : 1,
            "executionStages" : {
                "stage" : "FETCH",
                "nReturned" : 1,
                "executionTimeMillisEstimate" : 0,
                "works" : 2,
                "advanced" : 1,
                "needTime" : 0,
                "needYield" : 0,
                "saveState" : 0,
                "restoreState" : 0,
                "isEOF" : 1,
                "docsExamined" : 1,
                "alreadyHasObj" : 0,
                "inputStage" : {
                    "stage" : "IXSCAN",
                    "nReturned" : 1,
                    "executionTimeMillisEstimate" : 0,
                    "works" : 2,
                    "advanced" : 1,
                    "needTime" : 0,
                    "needYield" : 0,
                    "saveState" : 0,
                    "restoreState" : 0,
                    "isEOF" : 1,
                    "keyPattern" : {
                        "username" : 1
                    },
                    "indexName" : "username_index",
                    "isMultiKey" : false,
                    "multiKeyPaths" : {
                        "username" : [ ]
                    },
                    "isUnique" : false,
                    "isSparse" : false,
                    "isPartial" : false,
                    "indexVersion" : 2,
                    "direction" : "forward",
                    "indexBounds" : {
                        "username" : [
                            "[\"user1002\", \"user1002\"]"
                        ]
                    },
                    "keysExamined" : 1,
                    "seeks" : 1,
                    "dupsTested" : 0,
                    "dupsDropped" : 0
                }
            }
        }
        ```
        - Kết quả query cho thấy :
            - `"totalKeysExamined" : 1` : số index key sử dụng để query 
            - `"totalDocsExamined" : 1` : query chỉ cần quét `1` document do đã có index
            - `"executionTimeMillisEstimate" : 0` : tốn `0ms` để thực hiện query trên. Điều này cho thấy hiệu quả rõ rệt khi thực hiện truy vấn với index
#### **Index Size**
- Cú pháp kiểm tra index size trên toàn bộ DB : 
    ```
    > db.stats().indexSizes
    19632128
    ```
    - Kiểm tra tổng indexsize trên collection :
    ```
    > db.users.stats().totalIndexSize
    19632128
    ```
    - Kiểm tra indexSize chi tiết :
    ```
    > db.users.stats().indexSizes
    { "_id_" : 9859072, "username_index" : 9773056 }
    ```
    - Từ kết quả ta thấy tổng kích cỡ của index `username_index` là `9773056 Bytes` ~ `9.77MB`: 
        - Indexsize hiện tại còn quá thấp để làm tràn RAM. Tuy nhiên, trường hợp index size của index cần truy vấn vượt quá dung lượng RAM, khi sử dụng index mà index đó , nếu index đó không fit đủ trên RAM thì mongo sẽ phải đọc Index đó từ disk
        - Nói chung, cần xác định đủ lượng memory cần dùng cho server Mongo tùy vào các nhu cầu :
            - Độ lớn data 
            - Độ lớn index
            - Độ phát triển của dữ liệu (1 tháng, 1 năm)
            - Độ lớn của tập dữ liệu thường query
#### **Xóa các index dư thừa**
- Cú pháp kiểm tra số lần index được sử dụng :
    ```
    > db.users.aggregate([ { $indexStats: {} } ])
    { "name" : "username_index", "key" : { "username" : 1 }, "host" : "cuongnq-mongo-4.novalocal:27017", "accesses" : { "ops" : NumberLong(0), "since" : ISODate("2020-10-19T07:30:48.534Z") }, "spec" : { "v" : 2, "key" : { "username" : 1 }, "name" : "username_index", "background" : true } }
    { "name" : "_id_", "key" : { "_id" : 1 }, "host" : "cuongnq-mongo-4.novalocal:27017", "accesses" : { "ops" : NumberLong(31), "since" : ISODate("2020-10-19T04:34:08.309Z") }, "spec" : { "v" : 2, "key" : { "_id" : 1 }, "name" : "_id_" } }
    ```
    - Từ kết quả ta thấy :
        - Index `username_index` : `"ops" : NumberLong(0)` : `0` lần query index kể từ `since`
        - Index `_id` : `"ops" : NumberLong(31)` : `31` lần query index kể từ `since`
        > `since` ở đây sẽ là thời điểm index được khởi tạo hoặc thời điểm restart dịch vụ `mongod`
- Ta sẽ xóa bớt những index trùng lặp hoặc ít sử dụng để giảm thiểu bộ nhớ index ghi lên RAM mỗi lần truy vấn .
----------------
Tham khảo :
- https://docs.mongodb.com/manual/indexes/
- https://kipalog.com/posts/Toi-uu-hieu-suat-MongoDB-bang-cach-quan-ly-index
- https://viblo.asia/p/gioi-thieu-ve-indexes-trong-mongodb-E7bGo9oOR5e2