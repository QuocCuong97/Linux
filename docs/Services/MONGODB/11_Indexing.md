# Index trong Mongo
- **Index** hỗ trợ một cách hiệu quả cho các truy vấn. Không có **index**, **MongoDB** sẽ scan tất cả các document của collection để chọn các document match với điều kiện query. Quá trình truy vấn này rất kém hiệu quả và yêu cầu **MongoDB** phải xử lý một lượng lớn dữ liệu ,
- **Index** là một cấu trúc dữ liệu đặc biệt, lưu trữ 1 phần nhỏ của data set trong một dạng preview. **Index** sẽ lưu trữ giá trị của 1 field được chỉ định trong set các field, sắp xếp giá trị của field theo thứ tự chỉ định trước .
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
    
This method returns the description of all the indexes int the collection.


https://kipalog.com/posts/Toi-uu-hieu-suat-MongoDB-bang-cach-quan-ly-index
https://viblo.asia/p/gioi-thieu-ve-indexes-trong-mongodb-E7bGo9oOR5e2