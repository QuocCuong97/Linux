# Ngôn ngữ SQL
## **1) Khái niệm**
- **SQL - Structured Query Language** hay *ngôn ngữ truy vấn có cấu trúc* là một loại ngôn ngữ máy tính để lưu trữ , thao tác và truy xuất dữ liệu được lưu trữ trong một cơ sở dữ liệu quan hệ .
- **SQL** là ngôn ngữ chuẩn cho **RDMS - Relational Database Management System** ( *hệ cơ sở dữ liệu quan hệ* ) . Tất cả **RDMS** như **MySQL** , **MariaDB** , **MS Access** , **Oracle** , **Postgre** và **SQL Server** đều sử dụng **SQL** làm ngôn ngữ cơ sở dữ liệu chuẩn .
- Phiên bản mới nhất : `SQL:2016`
- **Ưu điểm :**
    - Cho phép người dùng truy cập dữ liệu trong các hệ thống quản lý cơ sở dữ liệu quan hệ.
    - Cho phép người dùng mô tả dữ liệu.
    - Cho phép người dùng xác định dữ liệu trong cơ sở dữ liệu và thao tác dữ liệu đó.
    - Cho phép nhúng trong các ngôn ngữ khác sử dụng mô-đun SQL, thư viện và trình biên dịch trước.
    - Cho phép người dùng tạo và thả các cơ sở dữ liệu và bảng.
    - Cho phép người dùng tạo chế độ view, thủ tục lưu trữ, chức năng trong cơ sở dữ liệu.
    - Cho phép người dùng thiết lập quyền trên các bảng, thủ tục và view .
- Các hệ quản trị cơ sở dữ liệu phổ biến :
    - **MySQL**
    - **MariaDB**
    - **MongoDB**
    - **Microsoft Access**
    - **Microsoft SQL Server**
    - **PostgreSQL**
    - **Oracle**
## **2) Các kiểu dữ liệu trong SQL**
- Kiểu dữ liệu **SQL** là một thuộc tính xác định Kiểu dữ liệu của bất kỳ đối tượng nào. Mỗi cột, biến và biểu thức có một Kiểu dữ liệu trong SQL. Bạn có thể sử dụng các Kiểu dữ liệu này trong khi tạo các bảng của bạn. Bạn có thể chọn Kiểu dữ liệu cho một cột của bảng dựa trên yêu cầu của bạn.
- **Kiểu dữ liệu số nguyên** :

    | Data Type | From | To |
    |-----------|------|----|
    | **`bigint(m)`** | `-9,223,372,036,854,775,808` ( <code>-2<sup>63</sup></code > ) | `9,223,372,036,854,775,807` ( <code>2<sup>63</sup></code>`-1` ) |
    | **`int(m)`** | `-2,147,483,648` ( <code>-2<sup>31</sup></code > ) | `2,147,483,647` ( <code>2<sup>31</sup></code>`-1` ) |
    | **`smallint(m)`** | `-32,768` ( <code>-2<sup>15</sup></code > ) | `32,767` ( <code>2<sup>15</sup></code>`-1` ) |
    | **`tinyint(m)`** | `0` | `255` |
    | **`bit(m)`** | `0` | `1` |
    | **`decimal(m)`** | <code>-10<sup>38</sup></code>`+1` | <code>10<sup>38</sup></code>`-1` |
    | **`numeric(m)`** | <code>-10<sup>38</sup></code>`+1` | <code>10<sup>38</sup></code>`-1` |
    | **`money(m)`** | `-922,337,203,685,477.5808` | `+922,337,203,685,477.5807` |
    | **`smallmoney(m)`** | `-214.748.3648` | `+214.748.3647` |

- **Kiểu dữ liệu số có dấu phẩy động** :

    | Data Type | From | To |
    |-----------|------|----|
    | **`float(m,d)`** | `-1.79E + 308` | `1,79E + 308` |
    | **`real(m,d)`** | `-3,40E + 38` | `3,40E + 38` |

- **Kiểu dữ liệu Thời gian**

    | Data Type | From | To |
    |-----------|------|----|
    | **`date` (`YYYY-MM-DD`)** | `1000-01-01` | `9999-12-31` |
    | **`time` (`HH:MM:SS`)** | `-838:59:59` | `838:59:59` |
    | **`datetime` (`YYYY-MM-DD HH:MM:SS`)** | `1000-01-01 00:00:00` | `9999-12-31 23:59:59` |
    | **`timestamp`** | `1970-01-01 00:00:01` | `2038-01-19 03:14:07` |

- **Kiểu dữ liệu chuỗi ký tự**

    | Data Type | Mô tả |
    |-----------|-------|
    | **`char(m)`** | Chiều dài tối đa `8.000` ký tự (cố định chiều dài ký tự không phải là ký tự Unicode) |
    | **`varchar(m)`** | Tối đa `8.000` ký tự (Dữ liệu không phải dạng Unicode có độ dài biến đổi) |
    | **`text(m)`** | Dữ liệu không phải dạng Unicode có chiều dài không thay đổi với độ dài tối đa là `2,147,483,647` ký tự

- **Kiểu dữ liệu chuỗi ký tự `Unicode`**

    | Data Type | Mô tả |
    |-----------|-------|
    | **`nchar(m)`** | Chiều dài tối đa `4.000` ký tự (Chiều dài cố định Unicode) |
    | **`nvarchar(m)`** | Chiều dài tối đa `4.000` ký tự (Chiều dài biến Unicode) |
    | **`ntxt(m)`** | Chiều dài tối đa là `1,073,741,823` ký tự (Chiều dài biến Unicode) |
## **3) Các phần tử trong Database**
- **TABLE** - Bảng : 
    - Dữ liệu trong một **RDBMS** được lưu trữ trong các đối tượng cơ sở dữ liệu được gọi là các bảng ( **table** ) . Bảng này về cơ bản là một bộ sưu tập các mục nhập dữ liệu có liên quan và nó bao gồm nhiều cột và hàng .
    - **VD** sau được gọi là 1 bảng .
        ```
        +----+----------+------+-----------+----------+
        | ID | TEN      | TUOI | DIA_CHI   | LUONG    |
        +----+----------+------+-----------+----------+
        |  1 | Nam      |  32  | Ha Noi    |  2000.00 |
        |  2 | Dung     |  25  | Vinh Phuc |  1500.00 |
        |  3 | Vinh     |  23  | Ha Noi    |  2000.00 |
        +----+----------+-----+------------+----------+
        ```
- **FIELD** - Trường
    - Mỗi bảng được chia thành các thực thể nhỏ gọi là các trường . Các trường trong bảng `KHACH_HANG` bao gồm `ID`, `TEN`, `TUOI`, `DIA_CHI` VÀ `LUONG` .
        ```
        +----+----------+------+-----------+----------+
        | ID | TEN      | TUOI | DIA_CHI   | LUONG    |
        +----+----------+------+-----------+----------+
        ```
    - Trường là một cột trong một bảng được thiết kế để lưu trữ thông tin cụ thể về mỗi bản ghi trong bảng.
- **ROW** - Hàng hoặc **RECORD** - Bản ghi
    - Một bản ghi cũng được gọi là một hàng dữ liệu là từng mục riêng lẻ tồn tại trong một bảng . Ví dụ: có 3 bản ghi trong bảng KHACH_HANG trên. 
        ```
        +----+----------+------+-----------+----------+
        |  1 | Nam      |  32  | Ha Noi    |  2000.00 |
        |  2 | Dung     |  25  | Vinh Phuc |  1500.00 |
        |  3 | Vinh     |  23  | Ha Noi    |  2000.00 |
        +----+----------+-----+------------+----------+
        ```
- **COLUMN** - Cột
    - Một cột là một thực thể thẳng đứng trong một bảng có chứa tất cả các thông tin liên kết với một trường cụ thể trong một bảng .
        ```
        +----------+
        | LUONG    |
        +----------+
        |  2000.00 |
        |  1500.00 |
        |  2000.00 |
        +----------+
        ```
> ### **Một số giá trị đặc biệt**
- **NULL** :
    - Một trường với một giá trị NULL là một trường không có giá trị.
    - Điều quan trọng là phải hiểu rằng một giá trị NULL khác với giá trị bằng không hoặc một trường có chứa khoảng trắng (space). Trường có giá trị NULL là giá trị đã để trống trong quá trình tạo bản ghi.
- **Constraint** (ràng buộc) trong SQL :
    - Constraint là các quy tắc được thi hành trên các cột dữ liệu trên một bảng. Chúng được sử dụng để giới hạn loại dữ liệu có thể insert vào một bảng. Điều này đảm bảo tính chính xác và độ tin cậy của dữ liệu trong cơ sở dữ liệu.
    - Constraint có thể là cấp độ cột hoặc cấp độ bảng. Các ràng buộc cấp độ cột chỉ được áp dụng cho một cột trong khi các ràng buộc mức bảng được áp dụng cho toàn bộ bảng.
    - Sau đây là một số các ràng buộc phổ biến nhất được sử dụng trong SQL:
        - `NOT NULL` – Đảm bảo rằng một cột không thể có giá trị NULL.
        - `DEFAULT` – Cung cấp một giá trị mặc định cho một cột khi không có gì được chỉ định.
        - `UNIQUE` – Đảm bảo rằng tất cả các giá trị trong một cột là khác nhau.
        - `PRIMARY Key` – Xác định mỗi hàng / bản ghi là duy nhất trong một bảng cơ sở dữ liệu.
        - `FOREIGN Key` – Xác định một hàng / bản ghi là duy nhất trong bất kỳ bảng cơ sở dữ liệu khác.
        -`CHECK` – CHECK constraint đảm bảo rằng tất cả các giá trị trong một cột thỏa mãn một số điều kiện.
        - `INDEX` – Dùng để tạo và lấy dữ liệu từ cơ sở dữ liệu rất nhanh.
## **4) Các câu lệnh trong SQL**
- ### **Nhóm lệnh 1 :** **`DDL`** – Ngôn ngữ định nghĩa dữ liệu ( ***Data Definition Language*** ) :
    - `CREATE` : Tạo ra một bảng mới, một view của một bảng, hoặc các đối tượng khác trong cơ sở dữ liệu
    - `ALTER` : Sửa đổi một đối tượng cơ sở dữ liệu hiện có, chẳng hạn như một bảng
    - `DROP` : Xoá toàn bộ một bảng, view của một bảng hoặc các đối tượng khác trong cơ sở dữ liệu
    - `RENAME` : dùng để đổi tên bảng hoặc 1 đối tượng trong cơ sở dữ liệu
- ### **Nhóm lệnh 2 :** **`DML`** – Ngôn ngữ thao tác dữ liệu ( ***Data Manipulation Language*** ) :
    - `SELECT` : Lấy ra các bản ghi nhất định từ một hoặc nhiều bảng
    - `INSERT` : dùng để thêm dữ liệu vào một bảng đã tồn tại
    - `UPDATE` : dùng để thay đổi giá trị của một tập hợp các bản ghi trong một bảng
    - `MERGE` : dùng để kết hợp dữ liệu của nhiều bảng . Nó được dùng như việc kết hợp giữa hai phần tử `INSERT` và `UPDATE` .
    - `DELETE` : xóa những bản ghi tồn tại trong một bảng
    - `TRUNCATE` : Xóa toàn bộ dữ liệu trong một bảng ( không phải là tiêu chuẩn , nhưng là một lệnh SQL phổ biến )
- ### **Nhóm lệnh 3 :** **`DCL`** – Ngôn ngữ điều khiển dữ liệu ( ***Data Control Language*** ) :
    - `GRANT` : Cung cấp một đặc quyền cho người dùng
    - `REVOKE` : Lấy lại các đặc quyền được cấp từ người dùng
- ### **Nhóm lệnh 4 :** **`TCL`** ( ***Transaction Control Language*** )
    - `COMMIT`
    - `ROLLBACK`
    - `SAVEPOINT`
    - `SET TRANSACTION`