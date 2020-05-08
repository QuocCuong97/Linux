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
    <img src=https://i.imgur.com/rUa3ajP.png>
- **B6 :** Kiểm tra xem tiến trình **MariaDB** có chạy không :
    ```
    # ps aux | grep -v "grep" | grep mysql
    ```
    <img src=https://i.imgur.com/TRMOBf5.png>
- **B7 :** Kiểm tra xem dịch vụ có listen port `3306` không :
    ```
    # ss -lntp | grep "3306"
    ```
    <img src=https://i.imgur.com/wAt4C9E.png>

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

        <img src=https://i.imgur.com/BvzJEym.png>

    - Tại tùy chọn sử dụng `unix_socket authentication` , chọn `n` :

        <img src=https://i.imgur.com/FFPZSj8.png>

    - Tại tùy `Change the root password` , chọn `Y` để đổi mật khẩu user `root` :

        <img src=https://i.imgur.com/5QykvAh.png>

    - Tại tùy chọn `Remove anonymous user` , chọn `Y` :

        <img src=https://i.imgur.com/lvgtL7q.png>

    - Tại tùy chọn `Disallow root login remotely` , chọn `Y` để không cho phép user `root` login từ xa vào dịch vụ :

        <img src=https://i.imgur.com/psBTeh5.png>

    - Tại tùy chọn `Remove test database and access to it` , chọn `Y` để xóa bỏ database "`test`" và quyền truy cập nó :

        <img src=https://i.imgur.com/3jXr0kh.png>

    - Tại tùy chọn `Reload privilege tables now` , chọn `Y` để reload lại các table liên quan đến quyền hạn :

        <img src=https://i.imgur.com/SGCycgo.png>

- **B2 :** Khởi động lại dịch vụ `mariadb` :
    ```
    # systemctl restart mariadb.service
    ```
    