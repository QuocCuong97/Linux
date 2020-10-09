# Các thao tác với Collection
### **TẠO COLLECTION**
- Cú pháp :
    ```
    > db.createCollection(name, options)
    ```
    - `name` : tên của collection cần tạo
    - `options` : là một document sử dụng để chỉ định các thông số cho collection. Các option không bắt buộc
- Các tham số option có thể sử dụng :
    - `capped` (Boolean) : Nếu được set là `true`, capped collection sẽ được kích hoạt. Capped collection là một collection có kích cỡ cố định và sẽ tự động ghi đè lên các document cũ hơn khi nó đạt tới kích cỡ tối đa. Nếu set là `true`, cần set thêm tham số `size` đi kèm .
    - `autoIndexId` (Boolean) : Nếu được set là `true`, mongo sẽ tự động tạo index trên trường `_id`. Mặc định là `false` 
    - `size` (Number) : Chỉ định kích cỡ tối đa tính bằng `bytes` của capped collection. (Bắt buộc nếu sử dụng capped collection)
    - `max` (Number) : Chỉ định số lượng document tối đa cho phép trong capped collection . Khi insert document, **MongoDB** sẽ check `size` của capped collection trước, sau đó check đến `max`
- **VD1 :** Tạo mới collection "`mycollection`" :
    ```
    > use mydb
    switched to db mydb
    > db.createCollection("mycollection")
    { "ok" : 1 }
    ```
    - Kiểm tra collection vừa tạo bằng lệnh `show collections` :
        ```
        > show collections
        movie
        mycollection
        ```
- **VD2 :** Tạo mới collection với các option đi kèm :
    ```
    > db.createCollection("mycols", { capped : true, size : 6142800, max : 10000 })
    { "ok" : 1 }
    ```
> Trong MongoDB, có thể bỏ qua bước tạo **collection** . Nó sẽ tự động được tạo khi insert một document vào database ,
### **XÓA COLLECTION CÓ SẴN**
- Cú pháp :
    ```
    > db.COLLECTION_NAME.drop()
    ```
- **VD :** Xóa collection "`mycollection`" :
    ```
    > db.mycollection.drop()
    true
    ```