# MySQL             
## **1) Tổng quan** <img src=https://i.imgur.com/8E7j8uM.png align=right width=25%>
- **MySQL** là một hệ thống quản trị cơ sở dữ liệu mã nguồn mở ( ***Relational Database Management System  - RDBMS*** ) hoạt động theo mô hình client-server . **RDBMS** là một phần mềm hay dịch vụ dùng để tạo và quản lý các cơ sở dữ liệu ( database ) theo hình thức quản lý các mối liên hệ giữa chúng .
- Lịch sử hình thành :
    - Công ty Thuy Điển **MySQL AB** được thành lập bởi `David Axmark` , `Allan Larsson` và `Michael "Monty" Widenius` phát triển **MySQL** vào năm `1994` . 
    - Công ty công nghệ Mỹ **Sun Microsystem** sau đó giữ quyền sở hữu **MySQL** sau khi mua lại **MySQL** vào năm `2008`  
    - Năm `2010` , gã khổng lồ **Oracle** mua **Sun Microsystems** và **MySQL** thuộc quyền sở hữu của **Oracle** từ đó .
- **MySQL** là 1 phần mềm mã nguồn mở miễn phí , sử dụng giấy phép **GNU Genenal Public License - GPLv2** .
- Trang chủ : https://www.mysql.com/
- Source Code : https://github.com/mysql/mysql-server
- Phiên bản đầu tiên phát hành ngày `23-5-1995`
- Phiên bản mới nhất hiện tại : `8.0.17` ( phát hành `22-7-2019` )
- Có 2 phiên bản **MySQL** :
    - **MySQL Cummunity** : phiên bản miễn phí
    - **MySQL Enterprise Edition** : phiên bản thương mại
- Là 1 thành phần của **LAMP Stack** - ( `Linux` , `Apache` , `MySQL` , `Perl/Python/PHP` )
## **2) Cách thức hoạt động**
- **B1 :** **MySQL** tạo ra bảng để lưu trữ dữ liệu, định nghĩa sự liên quan giữa các bảng đó .
- **B2 :** Client sẽ gửi yêu cầu **SQL** bằng một lệnh đặc biệt trên **MySQL** .
- **B3 :** Ứng dụng trên server sẽ phản hồi thông tin và trả về kết quả trên máy client .
## **3) Cài đặt MySQL `8.0`**
- **B1 :** Tải về **MySQL Repository** mới nhất :
    ```
    # wget http://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
    ```
- **B2 :** Cài đặt **MySQL Repository** : 
    ```
    # rpm -Uvh mysql80-community-release-el7-3.noarch.rpm
    ```
- **B3 :** Cài đặt `mysql-server` :
    ```
    # yum install -y mysql-server
    ```
- **B4 :** Khởi động dịch vụ `mysqld` :
    ```
    # systemctl start mysqld
    # systemctl enable mysqld
    ```
- **B5 :** Kiểm tra trạng thái dịch vụ :
    ```
    # systemctl status mysqld
    ```
