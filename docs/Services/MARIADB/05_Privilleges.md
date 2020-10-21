# Phân quyền Database cho User
> ## **Mục tiêu**
- Tạo ra các user mới và phân quyền thấp hơn user `root` để chia nhỏ công việc quản trị **Database** .
> ## **Các thao tác với User**
- **`CREATE`** user `truong_phong` trên `localhost` với password là `123456` :
    ```
    CREATE USER 'truong_phong'@'localhost' IDENTIFIED BY '123456';
    ```
    <img src=https://i.imgur.com/N6aYFXt.png>

    > Tại thời điểm tạo ra , user `truong_phong` không có bất cứ quyền gì , kể cả việc login vào trình quản lý của `mysql`
    - Có thể chia nhỏ bằng 2 câu lệnh :
        ```
        CREATE USER 'truong_phong'@'localhost'                           - > Tạo User
        SET PASSWORD for 'truong_phong'@'localhost'= password("123456"); - > Tạo Password
        ```
- **`SHOW`** các **User** hiện có :
    ```
    SELECT User from mysql.user;
    ```
    <img src=https://i.imgur.com/glMFP00.png>

    hoặc :
    ```
    SELECT User,Host from mysql.user;
    ```
    <img src=https://i.imgur.com/R6hc4eh.png>
- **`DELETE`** một **User** :
    ```
    DROP USER 'truong_phong'@'localhost'
    ```
> ## **Các thao tác phân quyền** 
- **`GRANT`** : Gán quyền cho **User** :
    ```
    GRANT [type of permission] ON [database_name].[table_name] TO '[username]'@'localhost';
    ```
    - **`Type of Permission`** :
        - `ALL PRIVILEGES` : cho phép MySQL user thực hiện toàn quyền trên **Databases** ( hoặc 1 vài db được thiết lập )
        - `CREATE` – Cho phép user tạo mới **Table** hoặc **Database**
        - `DROP` – Cho phép xóa **Table** hoặc **Database**
        - `DELETE` – Cho phép xóa bản ghi dữ liệu trong bảng **Tables**
        - `INSERT` – Cho phép thêm bản ghi mới vào **Table**
        - `SELECT` – Cho phép sử dụng lệnh `Select` để tìm kiếm dữ liệu
        - `UPDATE` – Cho phép cập nhật data
        - `GRANT OPTION` – Cho phép gán hoặc xóa quyền của người dùng khác
    - **`Database_name`** :
        - Để `*` nếu muốn cấp quyền user cho tất cả các **Database**
    - **`Table_name`** :
        - Để `*` nếu muốn cấp quyền user cho tất cả các **Table**
    - **VD1 :** Cấp full quyền cho user `truong_phong@localhost` :
        ```
        GRANT ALL PRIVILEGES ON * . * TO 'truong_phong'@'localhost';
        ```
        <img src=https://i.imgur.com/BbGORnL.png>

    - **VD2 :** Cấp quyền `SELECT` và `CREATE` cho user `truong_phong@localhost` :
        ```
        GRANT SELECT,CREATE ON * . * TO 'truong_phong'@'localhost';
- **`FLUSH`** : Refresh lại bảng quyền của user :
    ```
    FLUSH PRIVILEGES;
    ```
- **`SHOW`** quyền của user :
    ```
    SHOW GRANTS FOR [user_name]
    ```
    <img src=https://i.imgur.com/yoHU287.png>

- **`REVOKE`** : Thu hồi lại đặc quyền đã cấp cho user :
    ```
    REVOKE [type of permission] ON [database name].[table name] FROM '[username]'@'localhost';
    ```