# Cài đặt WordPress theo version
## **1) Cài đặt WordPress**
- **B1 :** Tạo mới cơ sở dữ liệu **MariaDB** cho **WordPress** :
    - Đăng nhập vào **MariaDB ( MySQL )** với quyền `root` :
        ```
        # mysql -u root -p
        Enter password:
        ```
    - Đi vào giao diện cấu hình **MariaDB** :

        <img src=https://i.imgur.com/AaVEx47.png>
    
    - Tạo 1 database mới tên "`wordpress`" :
        ```
        CREATE DATABASE wordpress;
        ```

        <img src=https://i.imgur.com/CerbFGP.png>

    - Tạo user và password để quản trị database :
        ```
        CREATE USER wordpressuser@localhost IDENTIFIED BY 'p@ssw0rd';
        ```
        <img src=https://i.imgur.com/gvdmcdS.png>

    - Cấp quyền quản trị database "`wordpress`" cho user "`wordpressuser`" :
        ```
        GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'p@ssw0rd';
        ```
        <img src=https://i.imgur.com/UhBS3Yy.png>

    - Flush ( refresh ) lại danh sách quyền trong **MariaDB** :
        ```
        FLUSH PRIVILEGES;
        ```
        <img src=https://i.imgur.com/OZDc21D.png>

    - Thoát khỏi **MariaDB** :
        ```
        exit
        ```
        <img src=https://i.imgur.com/7opnTfA.png>
- **B2 :** Cài đặt các module cần thiết :
    ```
    # yum install -y php-gd php-mysql
    ```
- **B3 :** Khởi động lại dịch vụ `httpd` để nhận module mới :
    ```
    # systemctl restart httpd
    ```
- **B4 :** Download **WordPress** và giải nén :
    - Phiên bản mới nhất :
    ```
    # wget https://wordpress.org/latest.tar.gz
    # tar -zxvf latest.tar.gz
    ```
    - Phiên bản cụ thể ( VD: `5.3.2` ) :
    ```
    # wget https://wordpress.org/wordpress-5.3.2.tar.gz
    # tar -zxvf wordpress-5.3.2.tar.gz
    ```
    <img src=https://i.imgur.com/174t8qf.png>

    > Làm tương tự với các phiên bản còn lại .

- **B5 :** Đồng bộ thư mục `wordpress` với thư mục `/var/www/html` . Lệnh `rsync` sẽ copy 1 cách an toàn các file/thư mục trong thư mục `wordpress` sang thư mục `/var/www/html` đồng thời giữ nguyên permission của chúng :
    ```
    # rsync -avP ~/wordpress/ /var/www/html/
    ```
    <img src=https://i.imgur.com/yBdI3xN.png>

- **B6 :** Tạo 1 thư mục mới để lưu trữ các file upload lên **WordPress** :
    ```
    # mkdir /var/www/html/wp-content/uploads
    ```
- **B7 :** Cấp quyền cho user `apache` đối với các thư mục do user `root` tạo ra :
    ```
    # chown -R apache:apache /var/www/html/
    ```
- **B8 :** Copy file cấu hình chính `wp-config.php` từ file mẫu :
    ```
    # cd /var/www/html/
    # cp wp-config-sample.php wp-config.php
    ```
- **B9 :** Chỉnh sửa file `wp-config.php` :    
    ```
    # vi wp-config.php
    ```
    - Kéo xuống dòng `21` và chỉnh sửa thông tin về **MariaDB** đã cấu hình ở trên :

        <img src=https://i.imgur.com/huOe5aI.png>

- **B10 :** Hoàn thành việc cài đặt **WordPress** thông qua trình duyệt :
    ```
    http://<web_server_IP>
    ```
    - Trang khởi động hiện ra , chọn ngôn ngữ > ***Continue*** :

        <img src=https://i.imgur.com/Bgjmmsj.png>

    - Nhập thông tin khởi tạo > ***Install WordPress*** :

        <img src=https://i.imgur.com/a7A4fAO.png>

    - Cài đặt thành công > ***Log in*** :

        <img src=https://i.imgur.com/NcqpqaI.png>

    - Nhập `username` và `password` vừa tạo > ***Log in***

        <img src=https://i.imgur.com/s6RuicS.png>

    - Trang quản trị **WordPress** ( *dashboard* ) :

        <img src=https://i.imgur.com/z2ghATI.png>

> Nội dung trang **WordPress** khi người dùng khác nhập IP lên trình duyệt :<br><br><img src=https://i.imgur.com/DIop5ok.png>

## **2) Update version WordPress**
- **B1 :** Kiểm tra version hiện tại của **WordPress** :
    - **C1 :** Kiểm tra bằng lệnh :
        ```
        # grep wp_version /var/www/html/wp-includes/version.php | awk -F "'" '{print $2}'
        ```
        ```
        5.3.2
        ```
    - **C2 :** Kiểm tra trên giao diện web : Truy cập URL `http://ip-server/wp-admin/about.php`
        
        <img src=https://i.imgur.com/9m242nk.png>

    > Phiên bản mới nhất hiện tại là `5.4` . Ta sẽ thực hiện update lên phiên bản này .
- **B2 :** Backup dữ liệu WordPress :
    ```
    # mkdir /root/backup_wordpress
    # cp -Rvf /var/www/html/* /root/backup_wordpress/
    ```
- **B3 :** Download WordPress `5.4` (hoặc phiên bản mới nhất) và giải nén :
    ```
    # cd /tmp
    # wget http://wordpress.org/latest.tar.gz
    # tar -zxf latest.tar.gz
    ```
    hoặc chỉ định phiên bản cụ thể :
    ```
    # cd /tmp
    # wget https://wordpress.org/wordpress-5.4.tar.gz
    # tar -zxf wordpress-5.4.tar.gz
    ```
- **B4 :** Xóa thư mục `wp-admin` và `wp-includes` :
    ```
    # rm -rf /var/www/html/wp-admin /var/www/html/wp-includes
    ```
- **B5 :** Sao chép các file và thư mục từ bản mới tải sang đường dẫn chứa wordpress :
    ```
    # mv -f /tmp/wordpress/* /var/www/html/
    ```
- **B6 :** Truy cập **WordPress** thông qua trình duyệt để hoàn tất việc update :
    ```
    http://<web_server_IP>/wp-admin
    ```
    <img src=https://i.imgur.com/glGzNrT.png>

    - Chọn ***Continue*** để kết thúc :

    <img src=https://i.imgur.com/t9WOkZ9.png>

- **B7 :** Kiểm tra lại phiên bản **WordPress** vừa cài đặt
    ```
    http://<web_server_IP>/wp-admin/about.php
    ```
    <img src=https://i.imgur.com/7TGLiXA.png>

- **B8 :** Kiểm tra lại dữ liệu các bài viết :
    ```
    http://<web_server_IP>
    ```
    <img src=https://i.imgur.com/DIop5ok.png>