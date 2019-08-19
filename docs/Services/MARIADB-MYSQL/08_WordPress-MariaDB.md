# Kết nối Database với WordPress ở 2 node khác nhau
## **1) Mục tiêu**
- Web Server cài WordPress trên nó và truy xuất database từ một Database Server cài MariaDB trên cùng một mạng LAN .
## **2) Mô hình**
<img src=https://i.imgur.com/xV9OyvL.png>

## **3) Cấu hình**
> ### **Trên Database Server**
- **B1 :** Cài đặt dịch vụ **MariadDB** :
    <details>
    <summary><i>Chi tiết</i></summary>

    - Khởi tạo thông tin repository **MariaDB** để chương trình `yum` biết nguồn tải cài đặt **MariaDB** :
        ```
        # vi /etc/yum.repos.d/MariaDB.repo
        ```
        - Thêm vào nội dung sau :
            ```
            [mariadb]
            name = MariaDB
            baseurl = http://yum.mariadb.org/10.4.7/centos7-amd64
            gpgkey=http://yum.mariadb.org/RPM-GPG-KEY-MariaDB
            gpgcheck=1
            ```
    - Cập nhật lại thông tin các repository :
        ```
        # yum repolist
        ```
    - Cài đặt **MariaDB** :
        ```
        # yum install MariaDB-server MariaDB-client MariaDB-devel -y
        ```
    - Tắt **SELinux** :
        ```
        # setenforce 0
        ```
    - Khởi động và cấu hình startup cho dịch vụ :
        ```
        # systemctl start mariadb.service
        # systemctl enable mariadb.service
        ```

    </details>
- **B2 :** Cấu hình dịch vụ **MariaDB**
    - Đăng nhập vào **MariaDB ( MySQL )** với quyền `root` :
        ```
        # mysql -u root -p
        Enter password:
        ```
    - Tạo 1 database mới tên "`wordpress`" :
        ```
        CREATE DATABASE wordpress;
        ```
    - Tạo user và password để cho Web Server quản trị database :
        ```
        CREATE USER 'webserver'@'192.168.5.30 IDENTIFIED BY 'P@ssw0rd';
        ```
        >`192.168.5.30` là IP của Web Server
    - Cấp quyền quản trị database "`wordpress`" cho user "`webserver@192.168.5.30`" :
        ```
        GRANT ALL PRIVILEGES ON wordpress.* TO 'webserver'@'192.168.5.30' IDENTIFIED BY 'P@ssw0rd';
        ```
    - Flush ( refresh ) lại danh sách quyền trong **MariaDB** :
        ```
        FLUSH PRIVILEGES;
        ```
    - Thoát khỏi **MariaDB** :
        ```
        exit
        ```
> ### **Trên Web Server**
- **B1 :** Cài đặt **Apache** :
    <details>
    <summary><i>Chi tiết</i></summary>

    - Cài đặt dịch vụ `httpd` :
        ```
        # yum install -y httpd
        ```
    - Khởi động và cấu hình startup cho dịch vụ :
        ```
        # systemctl start httpd
        # systemctl enable httpd
        ```
    - Cấu hình **firewalld** cho phép dịch vụ `http` :
        ```
        # firewall-cmd --zone=public --permanent --add-service=http
        # firewall-cmd --reload
        ```
    </details>

- **B2 :** Cài đặt **PHP** :
    <details>
    <summary><i>Chi tiết</i></summary>

    - Cài đặt repo **EPEL** :
        ```
        # yum install -y epel-release.noarch
        ```
    - Cài đặt gói `yum-utils`
        ```
        # yum install -y yum-utils
        ```
    - Cài repository của **REMI** :
        ```
        # wget https://rpms.remirepo.net/enterprise/remi-release-7.rpm
        ```
    - Cài đặt repo **REMI `7`** :
        ```
        # rpm -Uvh remi-release-7.rpm
        ```
    - Kích hoạt repo **REMI `7`** sử dụng cho **PHP `7.3`** :
        ```
        # yum-config-manager --disable remi-php54
        # yum-config-manager --enable remi-php73
        ```
    - Tiến hành cài đặt **PHP `7.3`** cùng 1 số module hỗ trợ :
        ```
        # yum -y install php
        ```
    - Khởi động lại dịch vụ **Apache** :
        ```
        # systemctl restart httpd
        ```
    </details>
- **B3 :** Cài đặt **WordPress** :
    <details>
    <summary><i>Chi tiết</i></summary>

    - Cài đặt các module cần thiết :
        ```
        # yum install -y php-gd php-mysql
        ```
    - Khởi động lại dịch vụ `httpd` để nhận module mới :
        ```
        # systemctl restart httpd
        ```
    - Tắt **SELinux** :
        ```
        # setenforce 0
        ```
    - Download phiên bản mới nhất của **WordPress** :
        ```
        # wget https://wordpress.org/latest.tar.gz
        ```
    - Giải nén thư mục tải về :
        ```
        # tar -zxvf latest.tar.gz
        ```
    - Đồng bộ thư mục `wordpress` với thư mục `/var/www/html` . Lệnh `rsync` sẽ copy 1 cách an toàn các file/thư mục trong thư mục `wordpress` sang thư mục `/var/www/html` đồng thời giữ nguyên permission của chúng :
        ```
        # rsync -avP ~/wordpress/ /var/www/html/
        ```
    - Tạo 1 thư mục mới để lưu trữ các file upload lên **WordPress** :
        ```
        # mkdir /var/www/html/wp-content/uploads
        ```
    - Cấp quyền cho user `apache` đối với các thư mục do user `root` tạo ra :
        ```
        # chown -R apache:apache /var/www/html/
        ```
    - Copy file cấu hình chính `wp-config.php` từ file mẫu :
        ```
        # cd /var/www/html/
        # cp wp-config-sample.php wp-config.php
        ```
    </details>
- **B4 :** Cấu hình **WordPress** :
    - Chỉnh sửa file `wp-config.php` :    
        ```
        # vi wp-config.php
        ```
        - Kéo xuống dòng `21` và chỉnh sửa thông tin về **MariaDB** đã cấu hình ở trên 

            <img src=https://i.imgur.com/VJpvtno.png>

- **B5 :** Truy cập Web qua Client :
    ```
    http://192.168.5.30
    ```
    <img src=https://i.imgur.com/MI0iF0j.png>