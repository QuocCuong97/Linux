# Cài đặt MongoDB
## **1) Cài đặt trên CentOS 7**
- **B1 :** Thêm repo của **MongoDB** :
    ```
    # vi /etc/yum.repos.d/mongodb-org.repo
    ```
    - Thêm vào đoạn sau :
        ```
        [mongodb-org-4.4]
        name=MongoDB Repository
        baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
        gpgcheck=1
        enabled=1
        gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
        ```
- **B2 :**
    ```
    # yum repolist
    ```
- **B3 :** Cài đặt **MongoDB** :
    ```
    # yum install -y mongodb-org
    ```
- **B4 :** 
    ```
    # vi /etc/security/limits.d/20-nproc.conf
    ```
    - Thêm vào cuối file :
        ```
        mongod     soft    nproc     32000
        ```
- **B5 :** Khởi động dịch vụ `mongod` :
    ```
    # systemctl enable mongod
    # systemctl start mongod
    ```
- **B6 :** Truy cập **MongoDB** :
    ```
    # mongo
    ```
    <img src=https://i.imgur.com/TaZeSn5.png>

## **2) Cài đặt trên Ubuntu**
- **B1 :** Update các package đang có :
    ```
    $ sudo apt-get update -y
    ```
- **B2 :** Cài đặt MongoDB :
    ```
    $ sudo apt install -y mongodb
    ```
- **B3 :** Khởi động dịch vụ và cho phép dịch vụ khởi động cùng hệ thống :
    ```
    $ sudo systemctl start mongodb
    $ sudo systemctl enable mongodb
    ```
- **B4 :** Truy cập Mongo :
    ```
    $ mongo
    MongoDB shell version v3.6.3
    connecting to: mongodb://127.0.0.1:27017
    MongoDB server version: 3.6.3
    Welcome to the MongoDB shell.
    For interactive help, type "help".
    For more comprehensive documentation, see
            http://docs.mongodb.org/
    Questions? Try the support group
            http://groups.google.com/group/mongodb-user
    Server has startup warnings:
    2020-10-09T09:18:12.693+0700 I STORAGE  [initandlisten]
    2020-10-09T09:18:12.693+0700 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
    2020-10-09T09:18:12.693+0700 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
    2020-10-09T09:18:14.889+0700 I CONTROL  [initandlisten]
    2020-10-09T09:18:14.889+0700 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
    2020-10-09T09:18:14.889+0700 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
    2020-10-09T09:18:14.889+0700 I CONTROL  [initandlisten]
    >
    ```
## **3) Cài đặt trên Windows**
- **B1 :** Download phiên bản mới nhất của **MongoDB** tại [trang chủ](https://www.mongodb.com/try/download/community?tck=docs_server) :
    
    <img src=https://i.imgur.com/xLoXkKS.png>

- **B2 :** Click vào file `mongodb-windows-x86_64-4.4.1-signed.msi` vừa tải về để cài đặt :

    <img src=https://i.imgur.com/FNkjF2z.png>

- **B3 :** Chọn ***Next*** :

    <p align=center><img src=https://i.imgur.com/Hji92TM.png width=50%></p>

- **B4 :** Chấp nhận điều khoản, chọn ***Next*** :

    <p align=center><img src=https://i.imgur.com/GYCAQrn.png width=50%></p>

- **B5 :** Chọn ***Complete*** :

    <p align=center><img src=https://i.imgur.com/rahGGeu.png width=50%></p>

- **B6 :** Chọn ***Next*** :

    <p align=center><img src=https://i.imgur.com/wL8HBKy.png width=50%></p>

- **B7 :** Chọn ***Next*** :

    <p align=center><img src=https://i.imgur.com/7qWiigw.png width=50%></p>

- **B8 :** Chọn ***Install*** để cài đặt :
    
    <p align=center><img src=https://i.imgur.com/mEQObeP.png width=50%></p>

- **B9 :** Trong quá trình cài đặt, phần mềm có thể yêu cầu khởi động lại máy, chọn ***OK*** để xác nhận khởi động lại :

    <p align=center><img src=https://i.imgur.com/TNTFAyc.png width=50%></p>

- **B10 :** Sử dụng **PowerShell** hoặc **CMD** để thao tác với mongo qua shell. Trước tiên cần tạo thư mục lưu trữ data của mongoDB :
    ```powershell
    C:\> md data-mongo
    C:\> md data-mongo\db
    ```
- **B11 :** Chỉ định đường dẫn data cho MongoDB :
    ```powershell
    C:\> cd 'C:\Program Files\MongoDB\Server\4.4\bin'
    C:\Program Files\MongoDB\Server\4.4\bin> .\mongod.exe --dbpath "C:\data-mongo"
    ```
- **B12 :** Khởi chạy mongoDB :
    ```powershell
    C:\Program Files\MongoDB\Server\4.4\bin> .\mongo.exe
    MongoDB shell version v4.4.1
    connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
    Implicit session: session { "id" : UUID("9e0be7b1-eea2-4f36-888f-c6b208daf97c") }
    MongoDB server version: 4.4.1
    Welcome to the MongoDB shell.
    For interactive help, type "help".
    For more comprehensive documentation, see
            https://docs.mongodb.com/
    Questions? Try the MongoDB Developer Community Forums
            https://community.mongodb.com
    ---
    The server generated these startup warnings when booting:
            2020-10-09T00:17:43.035+07:00: ***** SERVER RESTARTED *****
            2020-10-09T00:17:44.802+07:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
    ---
    ---
            Enable MongoDB's free cloud-based monitoring service, which will then receive and display
            metrics about your deployment (disk utilization, CPU, operation statistics, etc).

            The monitoring data will be available on a MongoDB website with a unique URL accessible to you
            and anyone you share the URL with. MongoDB may use this information to make product
            improvements and to suggest MongoDB products and deployment options to you.

            To enable free monitoring, run the following command: db.enableFreeMonitoring()
            To permanently disable this reminder, run the following command: db.disableFreeMonitoring()
    ---
    >
    ```
    > Khởi chạy thành công!

    > Các lần sau khi sử dụng Mongo cần 2 command sau :
    ```powershell
    C:\Program Files\MongoDB\Server\4.4\bin> .\mongod.exe --dbpath "C:\data-mongo"
    C:\Program Files\MongoDB\Server\4.4\bin> .\mongo.exe
    ```
