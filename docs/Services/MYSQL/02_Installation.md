# Cài đặt MariaDB
## **1) Cài đặt MariaDB `10.4.7`**
- **B1 :** Khởi tạo thông tin repository **MariaDB** để chương trình `yum` biết nguồn tải cài đặt **MariaDB** :
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
- **B2 :** Cập nhật lại thông tin các repository :
    ```
    # yum repolist
    ```
- **B3 :** Cài đặt **MariaDB** :
    ```
    # yum install MariaDB-server MariaDB-client MariaDB-devel -y
    ```
- **B4 :** Khởi động và cấu hình startup cho dịch vụ :
    ```
    # systemctl start mariadb.service
    # systemctl enable mariadb.service
    ```
- **B5 :** Kiểm tra phiên bản **MariaDB** :
    ```
    # mariadbd -V
    ```
    <img src=https://i.imgur.com/rbBVlrK.png>
- **B6 :** Kiểm tra xem tiến trình **MariaDB** có chạy không :
    ```
    # ps aux | grep -v "grep" | grep mysql
    ```
    <img src=https://i.imgur.com/6U2yxRR.png>
- **B7 :** Kiểm tra xem dịch vụ có listen port `3306` không :
    ```
    # ss -lntp | grep "3306"
    ```
    <img src=https://i.imgur.com/CTpG5WZ.png>