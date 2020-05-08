# Các thao tác cơ bản trên MariaDB
## **Mục lục**
- Thao tác với **DATABASE** :
    - [**`CREATE`** **DATABASE** mới]()
    - [**`SHOW`** bảng các **DATABASE**]()
    - [**`DELETE`** một **DATABASE**]()
    - [**`RENAME`** một **DATABASE**]()
    - [**`USE`** 1 **DATABASE** để làm việc]()
- Thao tác bên trong **DATABASE** :
    - [**`CREATE`** **TABLE** trong **DATABASE**]()
    - [**`SHOW`** **TABLES** trong **DATABASE**]()
    - [**`SHOW`** cấu trúc **TABLE**]()
    - [**`RENAME`** **TABLE**]()
    - [**`DELETE`** **TABLE**]()
- Thao tác bên trong **TABLE** :
    - [**`INSERT`** dữ liệu vào **TABLE**]()
    - [**`INSERT`** nhiều giá trị vào **TABLE**]()
    - [**`SHOW`** nội dung trong **TABLE**]()
    - [**`UPDATE`** dữ liệu trong **TABLE**]()
    - [**`DELETE`** 1 hàng ( ***row*** ) trong **TABLE**]()
    - [**`RENAME`** cột trong **TABLE**]()
    - [**`ADD`** cột vào sau cột cuối của **TABLE**]()
    - [**`ADD`** cột vào 1 vị trí cụ thể trong **TABLE**]()
    - [**`DELETE`** cột trong **TABLE**]()
    - [Sử dụng **`WHERE`** và **`LIKE`** để lọc dữ liệu trong **TABLE**]()
--------
>## **THAO TÁC VỚI DATABASE**
- Truy cập trình quản lý command của **MariaDB** , login bằng user `root` hoặc user đã được phân quyền bởi user `root` :
    ```
    # mysql -u root -p
    ```
    <img src=https://i.imgur.com/7r7hH8G.png>
- **`CREATE`** **DATABASE** mới :
    ```
    CREATE DATABASE <database_name>;
    ```
    <img src=https://i.imgur.com/VB1CpuN.png>

- **`SHOW`** bảng các **DATABASE** :
    ```
    SHOW DATABASES;
    ```
    <img src=https://i.imgur.com/r3h9QUl.png>

- **`DELETE`** một **DATABASE** :
    ```
    DROP DATABASE DatabaseName;
    ```
- **`RENAME`** một **DATABASE** :
    ```
    RENAME DATABASE OldDatabaseName to NewDatabaseName;
- **`USE`** 1 **DATABASE** để làm việc :
    ```
    USE <database_name>;
    ```
    <img src=https://i.imgur.com/E5t17f6.png>

>## **THAO TÁC BÊN TRONG DATABASE**
- **`CREATE`** **TABLE** trong **DATABASE** :
    ```
    CREATE TABLE tableName (columnName1 columnType1,columnName1 columnType2,....);
    ```
    - Trong đó :
        - `columnName` : tên các cột trong bảng
        - `columnType` : các thuộc tính của cột 
        - `PRIMARY KEY (columnName)` : cần set 1 cột trong bảng làm cột chính ( ***primary key*** ) , cột này không cho phép có giá trị `NULL`
    - **VD1 :** Tạo bảng `Book` với 2 cột `id` và `name` , với cột `id` là ***primary key*** :
        ```
        CREATE TABLE Book(
        id INT NOT NULL AUTO_INCREMENT,  
        name VARCHAR(100) NOT NULL,  
        PRIMARY KEY (id));
        ```
        <img src=https://i.imgur.com/ypyqYdx.png>
        
        > Thuộc tính `AUTO_INCREMENT` sẽ tự động tăng các giá trị của cột `id` lên `1` cho mỗi record mới được chèn vào bảng .
    - **VD2 :** Tạo bảng `Price` với 2 cột `id` và `price` , với cột `id` là ***primary key*** :
        ```
        CREATE TABLE Price(  
        id INT NOT NULL AUTO_INCREMENT,  
        price float NOT NULL,  
        PRIMARY KEY (id)); 
        ```
        <img src=https://i.imgur.com/zUwoRTv.png>
- **`SHOW`** **TABLES** trong **DATABASE** :
    ```
    SHOW TABLES;
    ```
    <img src=https://i.imgur.com/xudwg1j.png>

- **`SHOW`** cấu trúc **TABLE** ( hiển thị các thuộc tính ) :
    ```
    DESC TableName;
    ```
    - **VD1 :** Xem cấu trúc **TABLE** `Book` :
        ```
        DESC Book;
        ```
        <img src=https://i.imgur.com/3hGRbEl.png>

        - Trong đó :
            - `Field` : tên cột
            - `Type` : loại dữ liệu của cột
            - `Null` : có được phép để `NULL` không ( `NO` vì đã cấu hình `NO NULL` )
            - `Key - PRI` : đánh dấu cột `primary key` - cột đầu tiên của bảng
            - `Default` : giá trị mặc định của cột
            - `Extra - auto_increment` : các thuộc tính khác kèm theo
    - **VD2 :** Xem cấu trúc **TABLE** `Price` :
        ```
        DESC Price;
        ```
        <img src=https://i.imgur.com/yt4Dip8.png>

- **`RENAME`** **TABLE** :
    ```
    RENAME TABLE OldTableName to NewTableName;
    ```
    <img src=https://i.imgur.com/gO7D5iB.png>

- **`DELETE`** **TABLE** :
    ```
    DROP TABLE TableName;
    ```
    <img src=https://i.imgur.com/nHPdeRW.png>

> ## **THAO TÁC BÊN TRONG TABLE**
- **`INSERT`** dữ liệu vào **TABLE** :
    ```
    INSERT INTO tableName
    (column_1, column_2, ... )  
    VALUES  
    (value1, value2, ... ),  
    (value1, value2, ... ),  
    ...;  
    ```
    - **VD1 :** Insert dữ liệu vào **TABLE** `Book` :
        ```
        INSERT INTO book  
        (id, name)  
        VALUES(1, 'MariaDB Book');
        ```
        <img src=https://i.imgur.com/Nfc66zN.png>

    - **VD2 :** Insert dữ liệu vào **TABLE** `Price` :
        ```
        INSERT INTO price
        (id, price)  
        VALUES(1, 200);  
        ```
        <img src=https://i.imgur.com/QpKpWUL.png>

- **`SHOW`** nội dung trong **TABLE** :
    ```
    SELECT * from TableName
    ```
    - **VD :** Xem nội dung trong **TABLE** `Book` :
        ```
        SELECT * from Book
        ```
        <img src=https://i.imgur.com/KMKW89x.png>

- **`INSERT`** nhiều giá trị vào **TABLE** :
    - **VD :** Nhập nhiều giá trị cùng lúc vào **TABLE** `Book` :
        ```
        INSERT INTO Book
        (id, name)  
        VALUES  
        (2,'MariaDB Book2'),  
        (3,'MariaDB Book3'),  
        (4,'MariaDB Book4'),  
        (5,'MariaDB Book5');
        ```
        <img src=https://i.imgur.com/0FzpA0c.png>
        <img src=https://i.imgur.com/9LDXFUY.png>

- **`UPDATE`** dữ liệu trong **TABLE** :
    ```
    UPDATE tableName SET field=newValue, field2=newValue2,...  
    [WHERE ...]  
    ```
    - Trong đó : 
        - `SET field=newValue` : set giá trị mới cho ô cần chỉnh sửa 
        - `WHERE` : tham chiếu vị trí của ô cần chỉnh sửa
    - **VD1 :** Chỉnh sửa giá trị `MariaDB Book` - > `MariaDB Book1` trong **TABLE** `Book` :
        ```
        UPDATE Book
        SET name = 'MariaDB Book1'
        WHERE id = 1;
        ```
        <img src=https://i.imgur.com/TNCYQvN.png>
        <img src=https://i.imgur.com/905jyWn.png>

    - **VD2 :** Chỉnh sửa giá trị `MariaDB Book` - > `MariaDB Book1` và `1` -> `01` trong **TABLE** `Book` :
        ```
        UPDATE Book
        SET id = 01,
        name = 'MariaDB Book1'
        WHERE id = 1;
        ```
- **`DELETE`** 1 hàng ( ***row*** ) trong **TABLE** :
    ```
    DELETE FROM tableName  
    [WHERE condition(s)]  
    [ORDER BY exp [ ASC | DESC ]]  
    [LIMIT numberRows];   
    ```
    - **VD1 :** Xóa dòng có `id` = `5` của **TABLE** `Book` :
        ```
        DELETE FROM Book
        WHERE id = 5;
        ```
        <img src=https://i.imgur.com/PacjHjl.png>
    
- **`WHERE`** : sử dụng để lọc chính xác dòng cần xem , chỉnh sửa :
    ```
    SELECT * form Book
    WHERE id < 3;
    ```
    <img src=https://i.imgur.com/nXASZwl.png>

    > Có thể sử dụng `AND` hoặc `OR` để tăng thêm điều kiện lọc cho `WHERE`
- **`LIKE`** : được sử dụng để xác định mẫu dữ liệu khi truy cập **TABLE**  trong đó cần khớp chính xác . Nó có thể được kết hợp với các câu lệnh **`INSERT`**, **`UPDATE`** , **`SELECT`** và **`DELETE`** .

Bạn nên chuyển mẫu dữ liệu bạn đang tìm kiếm cho mệnh đề và nó sẽ trả về đúng hoặc sai. Dưới đây là các ký tự đại diện có thể được sử dụng cùng với mệnh đề:
https://www.guru99.com/mariadb-tutorial-install.html
- **`RENAME`** cột trong **TABLE** :
    ```
    ALTER TABLE TableName CHANGE OldColumnName NewColumnName NewDataType;
    ```
    - **VD :** Đổi tên cột `id` thành cột `STT` trong bảng `Book` :
        ```
        ALTER TABLE Book CHANGE id STT int;
        ```
        <img src=https://i.imgur.com/5QUnbOT.png>

- **`ADD`** cột vào sau cột cuối của **TABLE** :
    ```
    ALTER TABLE TableName ADD NewColumnName DataType;
    ```
    - **VD :** Thêm cột `Description` vào bảng `Book` :
        ```
        ALTER TABLE Book ADD Description VARCHAR(100);
        ```
        <img src=https://i.imgur.com/kjj0qeQ.png>

- **`ADD`** cột vào 1 vị trí cụ thể trong **TABLE** :
    ```
    ALTER TABLE table ADD NewColumnName Data_Type [FIRST|AFTER existing_column];
    ```
    - **VD :** Thêm cột `Price` vào giữa cột `name` và `Description` của bảng `Book` :
        ```
        ALTER TABLE Book ADD Price Float(6,6) AFTER name;
        ```
        <img src=https://i.imgur.com/PMTWdvB.png>

- **`DELETE`** cột trong **TABLE** :
    ```
    ALTER TABLE TableName DROP COLUMN ColumnName;
    ```
    - **VD :** Xóa cột `Price` vừa tạo trong bảng `Book` :
        ```
        ALTER TABLE Book DROP COLUMN Price;
        ```
        <img src=https://i.imgur.com/tIi1gCG.png>

