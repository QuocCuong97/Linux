# Cài đặt MariaDB 10.4.8 trên CentOS 8
- **B1 :** Update hệ thống :
    ```
    yum -y update
    yum -y upgrade
    ```
- **B2 :** Cài đặt `galera` ( yêu cầu bắt buộc để cài `MariaDB 10.4.8` ) :
    ```
    yum install -y galera
    ```
- **B3 :** Disable repo `CentOS-AppStream` :
    ```
    vi /etc/yum.repos.d/CentOS-AppStream.repo
    ```
    - Sửa `enabled=1` thành `enabled=0` :
    ```
    [AppStream]
    name=CentOS-$releasever - AppStream
    mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=AppStream&infra=$infra
    #baseurl=http://mirror.centos.org/$contentdir/$releasever/AppStream/$basearch/os/
    gpgcheck=1
    enabled=0                
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
    ```
- **B4 :** Khởi tạo thông tin repository **MariaDB** để chương trình `yum` biết nguồn tải cài đặt **MariaDB** :
    ```
    vi /etc/yum.repos.d/MariaDB.repo
    ```
    - Thêm vào nội dung sau :
        ```
        [mariadb]
        name = MariaDB
        baseurl = http://yum.mariadb.org/10.4.8/centos8-amd64
        gpgkey=http://yum.mariadb.org/RPM-GPG-KEY-MariaDB
        gpgcheck=1
        ```
- **B5 :** Cập nhật lại thông tin các repository :
    ```
    yum repolist
    ```
- **B6 :** Cài đặt **MariaDB** :
    ```
    yum install MariaDB-server MariaDB-client MariaDB-devel -y
    ```
- **B7 :** Khởi động và cấu hình startup cho dịch vụ :
    ```
    systemctl start mariadb.service
    systemctl enable mariadb.service
    ```
- **B8 :** Kiểm tra phiên bản **MariaDB** :
    ```
    mariadbd -V
    ```
    <img src=https://i.imgur.com/vV2WqBr.png>
- **B9 :** Kiểm tra xem tiến trình **MariaDB** có chạy không :
    ```
    ps aux | grep -v "grep" | grep mysql
    ```
    <img src=https://i.imgur.com/vzAd7KJ.png>
- **B10 :** Kiểm tra xem dịch vụ có listen port `3306` không :
    ```
    ss -lntp | grep "3306"
    ```
    <img src=https://i.imgur.com/IvHAR4v.png>

- **B11 :** Sau khi cài đặt thành công `MariaDB 10.4.8` , có thể `enable` lại repo `CentOS-AppStream` :
    ```
    vi /etc/yum.repos.d/CentOS-AppStream.repo
    ```
    - Sửa `enabled=0` thành `enabled=1` :
    ```
    [AppStream]
    name=CentOS-$releasever - AppStream
    mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=AppStream&infra=$infra
    #baseurl=http://mirror.centos.org/$contentdir/$releasever/AppStream/$basearch/os/
    gpgcheck=1
    enabled=1               
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
    ```
- **B12 :** Cập nhật lại các repo :
    ```
    yum repolist
    ```