# Cài đặt PostgreSQL
## **1) Cài đặt trên CentOS 7**
- **B1 :** Cài đặt các package cần thiết của **Postgre** :
    ```
    # yum install -y postgresql-server postgresql-contrib
    ```
- **B2 :** Khởi tạo database :
    ```
    # postgresql-setup initdb
    Initializing database ... OK
    ```
- **B3 :** Kích hoạt dịch vụ và cho phép khởi động cùng hệ thống :
    ```
    # systemctl start postgresql
    # systemctl enable postgresql
    ```
- **B1 :** Chỉnh sửa repo của **CentOS 7** :
    - Thêm 2 dòng `exclude=postgresql*` ở phần `[base]` và `[updates]` như sau :
        ```ini
        [base]
        name=CentOS-$releasever - Base
        mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
        #baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        exclude=postgresql*

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
        #baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        exclude=postgresql*
        ....
        ```
- **B2 :** Cài đặt **postgresql** :
    ```
    # yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
    ```
- **B3 :** Liệt kê các packgage **posgresql** có sẵn :
    ```
    # yum list postgresql*
    ``` 
- **B4 :** Cài đặt phiên bản đã chọn :
    ```
    # yum install -y postgresql13-server postgresql13-contrib
    ```
- **B5 :**
    ```
    # /usr/pgsql-13/bin/postgresql-13-setup initdb
    ```
- **B6 :**
    ```
    # systemctl start postgresql-13
    # systemctl enable postgresql-13
    ```