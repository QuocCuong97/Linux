# Cài đặt WordPress
- **B1 :** Tạo mới cơ sở dữ liệu **MariaDB** cho **WordPress** :
- **B2 :** Cài đặt module `php-gd` :
    ```
    # yum install -y php-gd
    ```
- **B3 :** Khởi động lại dịch vụ `httpd` để nhận module mới :
    ```
    # systemctl restart httpd
    ```
- **B4 :** Download phiên bản mới nhất của **WordPress** :
    ```
    # wget https://wordpress.org/latest.tar.gz
    ```
- **B5 :** Giải nén thư mục tải về :
    ```
    # tar -zxvf latest.tar.gz
    ```
    <img src=https://i.imgur.com/174t8qf.png>

- **B6 :** Đồng bộ thư mục `wordpress` với thư mục `/var/www/html` . Lệnh `rsync` sẽ copy 1 cách an toàn các file/thư mục trong thư mục `wordpress` sang thư mục `/var/www/html` đồng thời giữ nguyên permission của chúng :
    ```
    # rsync -avP ~/wordpress/ /var/www/html/
    ```
    <img src=https://i.imgur.com/yBdI3xN.png>

- **B7 :** Tạo 1 thư mục mới để lưu trữ các file upload lên **WordPress** :
    ```
    # mkdir /var/www/html/wp-content/uploads
    ```
- **B8 :** Cấp quyền cho user `apache` đối với các thư mục do user `root` tạo ra :
    ```
    # chown -R apache:apache /var/www/html/
    ```
- **B9 :** Copy file cấu hình chính `wp-config.php` từ file mẫu :
    ```
    # cd /var/www/html/
    # cp wp-config-sample.php wp-config.php
    ```
- **B10 :** Chỉnh sửa file `wp-config.php` :    
    ```
    # vi wp-config.php
    ```
    https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-centos-7