# Quy trình cài đặt MariaDB trên RedHat 7 (standalone)
## **1) Cài đặt MariaDB**
- **B1 :** Khởi tạo thông tin repository **MariaDB** để chương trình `yum` biết nguồn tải cài đặt **MariaDB** :
    ```
    # vi /etc/yum.repos.d/MariaDB.repo
    ```
    - Thêm vào nội dung sau :
        ```
        [mariadb]
        name = MariaDB
        baseurl = https://mirrors.bkns.vn/mariadb/yum/10.5/rhel7-amd64
        gpgkey=https://mirrors.bkns.vn/mariadb/yum/RPM-GPG-KEY-MariaDB
        gpgcheck=1
        ```
- **B2 :** Cập nhật lại thông tin các repository :
    ```
    # yum repolist
    ```
- **B3 :** Cài đặt **MariaDB** :
    ```
    # yum install MariaDB-server MariaDB-client -y
    ```
- **B4 :** Khởi động và cấu hình startup cho dịch vụ :
    ```
    # systemctl start mariadb
    # systemctl enable mariadb
    ```
- **B5 :** Kiểm tra trạng thái dịch vụ :
    ```
    # systemctl status mariadb
    ```
    <img src=https://i.imgur.com/lTEQdc7.png>

- **B6 :** Kiểm tra phiên bản MariaDB đã cài đặt :
    ```
    # mariadb -V
    mariadb  Ver 15.1 Distrib 10.5.10-MariaDB, for Linux (x86_64) using readline 5.1
    ```
## **2) Thiết lập cấu hình bảo mật cơ bản cho dịch vụ MariaDB**
- Mục tiêu :
    - Thay đổi mật khẩu `root`
    - Xóa bỏ user `anonymous`
    - Tắt tính năng cho phép `root` login từ ngoài hệ thống
    - Xóa bỏ database "`test`" và quyền truy cập nó
    - Reload lại các table liên quan đến quyền hạn
- **B1 :** Thực hiện lệnh :
    ```
    # mysql_secure_installation
    ```
    - Chương trình yêu cầu nhập password user `root` :

        <img src=https://i.imgur.com/V3CqCpE.png>

    - Tại tùy chọn sử dụng `unix_socket authentication` , chọn `n` :

        <img src=https://i.imgur.com/Gg8l7bM.png>

    - Tại tùy chọn `Change the root password` , chọn `Y` để đổi mật khẩu user `root` :

        <img src=https://i.imgur.com/2hUBrZQ.png>

    - Tại tùy chọn `Remove anonymous user` , chọn `Y` :

        <img src=https://i.imgur.com/Gby1z45.png>

    - Tại tùy chọn `Disallow root login remotely` , chọn `Y` để không cho phép user `root` login từ xa vào dịch vụ :

        <img src=https://i.imgur.com/LdNfrUH.png>

    - Tại tùy chọn `Remove test database and access to it` , chọn `Y` để xóa bỏ database "`test`" và quyền truy cập nó :

        <img src=https://i.imgur.com/1xUxVgN.png>

    - Tại tùy chọn `Reload privilege tables now` , chọn `Y` để reload lại các table liên quan đến quyền hạn :

        <img src=https://i.imgur.com/dz9Cb0f.png>

- **B2 :** Khởi động lại dịch vụ `mariadb` :
    ```
    # systemctl restart mariadb
    ```
## **3) Một số thao tác cơ bản**
- Khởi động lại dịch vụ `mariadb` :
    ```
    # systemctl restart mariadb
    ```
- Dừng dịch vụ `mariadb` :
    ```
    # systemctl stop mariadb
    ```
- Ngừng cho phép dịch vụ khởi động cùng hệ thống :
    ```
    # systemctl disable mariadb
    ```
<!-- - Gỡ cài đặt MongoDB :
    ```
    # sudo yum erase $(rpm -qa | grep mongodb-enterprise)
    ```
    - Xóa các thư mục database và log :
        ```
        # sudo rm -r /var/log/mongodb
        # sudo rm -r /var/lib/mongo
        ``` -->
## **4) Các thao tác Backup/Restore**
### **4.1) Backup với `mysqldump`**
- Cấu trúc lệnh :
    ```
    # mysqldump -u [username] -p [databaseName] > [filename]-$(date +%F).sql
    ```
- `mysqldump` sẽ hỏi mật khẩu của user có quyền sao lưu .
- Dựa vào kích cỡ của **Database** , có thể sẽ mất 1 lúc để hoàn thành .
- **Database** được backup sẽ được tạo ở thư mục hiện hành khi thực hiện lệnh .
- `-$(date +%F)` thêm mốc thời gian bakup cho file .
### **4.2) Backup thủ công**
- **B1 :** Dừng dịch vụ `mysql` :
    ```
    # systemctl stop mysql
    ```
- **B2 :** Tạo thư mục chứa file backup :
    ```
    # mkdir /opt/db-backups
    ```
- **B3 :** Gom tất cả các file **Database** cần backup vào 1 file `tar` :
    ```
    # tar -cfvz /opt/db-backups/db-$(date +%F).tar.gz /var/lib/mysql/*
    ```
- **B4 :** Khởi động lại dịch vụ :
    ```
    # systemctl restart mysql
    ```
### **4.3) Khôi phục từ các bản Backup `.sql`**
- Cấu trúc lệnh :
    ```
    # mysql -u [username] -p [databaseName] < [filename].sql
    ```
- **TH1 :** Khôi phục toàn bộ các **Database** sau khi được full-backup :
    ```
    # mysql -u root -p < full-backup.sql
    ```
- **TH2 :** Khôi phục 1 **Database** cụ thể :
    ```
    # mysql -u root -p mariadbtest < mariadbtest-backup.sql
    ```
- **TH3 :** Khôi phục 1 **Table** cụ thể trong **Database** :
    ```
    # mysql -u root -p name < mariadbtest-name-backup.sql
    ```
### **4.4) Khôi phục từ các bản Backup `.tar.gz`**
- **B1 :** Dừng dịch vụ `mysql` :
    ```
    # systemctl stop mysql
    ```
- **B2 :** Giải nén thư mục chứa file backup :
    ```
    # tar zxvf /opt/db-backups/db-archive.tar.gz -C /tmp/db/
    ```
- **B3 :** Backup thư mục lưu trữ **Database** cũ :
    ```
    # mv /var/lib/mysql /var/lib/mysql-old
    # mkdir /var/lib/mysql
    ```
- **B4 :** Copy file từ thư mục vừa giải nén vào thư mục chứa **Database** :
    ```
    # mv /tmp/db/* /var/lib/mysql
    ```
- **B5 :** Cấp quyền lại cho thư mục **Database** :
    ```
    # chown -R mysql:mysql /var/lib/mysql
    ```
- **B6 :** Restart lại dịch vụ :
    ```
    # systemctl restart mysql
    ```