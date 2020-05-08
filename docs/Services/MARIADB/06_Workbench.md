# Quản lý Database từ xa với Workbench
## **1) Workbench là gì?**
- **MySQL Workbench** là một công cụ truy cập cơ sở dữ liệu được mô hình hóa và thiết kế trực quan sử dụng cho cơ sở dữ liệu quan hệ **MySQL server** . **MySQL Workbench** giúp tạo ra các mô hình dữ liệu vật lý mới và hỗ trợ sửa đổi các cơ sở dữ liệu **MySQL** hiện có với các kỹ thuật đảo ngược / chuyển tiếp , các chức năng quản lý tùy chọn .
## **2) Cài đặt và kết nối Workbench**
<img src=https://i.imgur.com/COtp9Bj.png>

> ### **Trên Database Server**
- **B1 :** Cài đặt **MariaDB** :
    [*Hướng dẫn cài đặt dịch vụ **MariaDB***](https://github.com/QuocCuong97/Linux/blob/master/docs/Services/MARIADB-MYSQL/04_Install_MariaDB.md)
- **B2 :** Truy cập trình quản lý **MariaDB** :
    ```
    # mysql -u root
    ```
- **B3 :** Tạo user và ủy quyền trong Database :
    ```
    > CREATE USER 'workbench'@'%' identified by 'P@ssw0rd';
    > GRANT ALL PRIVILEGES ON *.* TO 'workbench'@'%';
    > FLUSH PRIVILEGES;
    > exit
    ```
- **B4 :** Chỉnh sửa file `server.cnf` :
    ```
    # vi  /etc/my.cnf.d/server.cnf
    :set nu
    ```
    - Tại dòng `45` , thêm vào bên dưới :
        ```
        bind-address=0.0.0.0
        ```

        <img src=https://i.imgur.com/D1w5gRN.png>

- **B5 :** Tắt **firewalld** :
    ```
    # systemctl stop firewalld
    # systemctl disable firewalld
    ```
- **B6 :** Khởi động lại dịch vụ :
    ```
    # systemctl restart mariadb.service
    ```
> ### **Trên Windows Client**
- **B1 :** Download **MySQL Workbench** :
    [Link Download]()
- **B2 :** Sau khi download file `mysql-workbench-community-8.0.17-winx64` , tiến hành cài đặt :

    <img src=https://i.imgur.com/MhwTEtG.png>

    <img src=https://i.imgur.com/Ncv7Uga.png>

    <img src=https://i.imgur.com/KU4OEHR.png>

    <img src=https://i.imgur.com/6opDZm3.png>

    <img src=https://i.imgur.com/bR64Pc3.png>

    Giao diện của **Workbench** sau khi cài đặt :
    
    <img src=https://i.imgur.com/iQhleRI.png>

- **B3 :** Click vào dấu "`+`" để tạo phiên kết nối mới đến **Database Server** :

    <img src=https://i.imgur.com/aLMJUUM.png>

- **B4 :** Nhập các thông tin của Database Server để kết nối đến từ xa :

    <img src=https://i.imgur.com/QsG2BRJ.png>

- **B5 :** Kết nối thành công . Chọn ***Continue Anyway*** :

    <img src=https://i.imgur.com/zqQtqEL.png>

- **B6 :** Giao diện chính của trình quản lý **Workbench** :

    <img src=https://i.imgur.com/Bqre8pm.png>

## **3) Các thao tác quản trị Database trên Workbench**
### **3.1) Tạo Database**
- **B1 :** Click vào biểu tượng <img src=https://i.imgur.com/2M5CNdW.png width=4%> trên thanh công cụ :

    <img src=https://i.imgur.com/uEMRaZr.png>

- **B2 :** Nhập tên Database muốn tạo :

    <img src=https://i.imgur.com/ZwgHna9.png>

- **B3 :** Xác nhận lệnh tạo Database :

    <img src=https://i.imgur.com/edyj0dy.png>

- **B4 :** Database sau khi tạo thành công sẽ xuất hiện ở góc màn hình , tại tab **Schemas** :

    <img src=https://i.imgur.com/XT4tR3e.png>

### **3.2) Tạo Table**
- **B1 :** Chuột phải vào ***Tables*** > ***Create Table*** :

    <img src=https://i.imgur.com/zibb7cA.png>

- **B2 :** Nhập các thông tin cho Table : `Table Name` , `Column Name` ,... , sau đó ***Apply*** để hoàn tất :

    <img src=https://i.imgur.com/TnI6t6l.png>

- **B3 :** Chọn ***Apply*** để xác nhận các thao tác tạo bảng :

    <img src=https://i.imgur.com/QnXtXMY.png>

- **B4 :** Tạo bảng thành công :

    <img src=https://i.imgur.com/GVGQzas.png>

### **3.3) Nhập dữ liệu vào Table**
### **3.4) Backup Database**
- **B1 :** Trong tab **Administration** , chọn ***Data Export*** :

    <img src=https://i.imgur.com/7hJTsnn.png>

- **B2 :** Nhập thông tin Database cần backup :

    <img src=https://i.imgur.com/O8NEVv9.png>

    - `Export to Dump Project Folder` : export từng table thành từng file khác nhau trong 1 folder .
    - `Export to Self-Contained File` : export tất cả thành 1 file `.sql`
### **3.5) Restore Database**
- **B1 :** Trong tab **Administration** , chọn ***Data Import/Restore*** :

    <img src=https://i.imgur.com/XTuPXVd.png>

- **B2 :** Nhập thông tin file/folder để Restore :

    <img src=https://i.imgur.com/2ILQq0J.png>

### **3.6) Tạo,chỉnh sửa quyền cho user**
- **B1 :** Trong tab **Administration** , chọn ***Users and Privileges*** :

    <img src=https://i.imgur.com/PfIw5UA.png>

- **B2 :** Chỉnh sửa quyền của các user đã tồn tại trong tab ***Administrative Roles*** :

    <img src=https://i.imgur.com/V2Ngvs6.png>

- **B3 :** Tạo user , nhập các thông tin cần thiết :

    <img src=https://i.imgur.com/h5ZDRlN.png>