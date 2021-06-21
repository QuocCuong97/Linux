# Quy trình cài đặt MongoDB trên RedHat 7 (standalone)
## **1) Cài đặt MongoDB**
- **B1 :** Thêm repo của **MongoDB** :
    ```
    # vi /etc/yum.repos.d/mongodb-enterprise-4.4.repo
    ```
    - Thêm vào đoạn sau:
        ```
        [mongodb-enterprise-4.4]
        name=MongoDB Enterprise Repository
        baseurl=https://repo.mongodb.com/yum/redhat/$releasever/mongodb-enterprise/4.4/$basearch/
        gpgcheck=1
        enabled=1
        gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
        ```
- **B2 :** Cập nhật repo vừa add :
    ```
    # yum repolist
    ```
- **B3 :** Cài đặt MongoDB :
    ```
    # sudo yum install -y mongodb-enterprise
    ```
- **B4 :** Khởi động MongoDB và cho phép dịch vụ khởi động cùng hệ thống:
    ```
    # sudo systemctl start mongod
    # sudo systemctl enable mongod
    ```
- **B5 :** Truy cập MongoDB :
    ```
    # mongod
    ```
    <img src=https://i.imgur.com/qtfNyFb.png>

## **2) Một số thao tác cơ bản**
- Khởi động lại dịch vụ `mongod` :
    ```
    # systemctl restart mongod
    ```
- Dừng dịch vụ `mongod` :
    ```
    # systemctl stop mongod
    ```
- Ngừng cho phép dịch vụ khởi động cùng hệ thống :
    ```
    # systemctl disable mongod
    ```
- Gỡ cài đặt MongoDB :
    ```
    # sudo yum erase $(rpm -qa | grep mongodb-enterprise)
    ```
    - Xóa các thư mục database và log :
        ```
        # sudo rm -r /var/log/mongodb
        # sudo rm -r /var/lib/mongo
        ```
## **3) Các thao tác Backup/Restore**
### **3.1) Backup DB**
- Để tạo backup cho database trong MongoDB, nên sử dụng lệnh `mongodump`. Lệnh này sẽ dump toàn bộ dữ liệu của server vào một thư mục được chỉ định sẵn.
- Cú pháp :
    ```
    # mongodump
    ```
    - Lệnh này sẽ kết nối server local (`127.0.0.1:27017`) và lưu tất cả data của server vào thư mục `dump/` trong thư mục hiện hành
- Có nhiều tùy chọn sẵn có giúp giới hạn lượng dữ liệu cần backup :
    - `mongodump --host HOST_NAME --port PORT_NUMBER` : backup tất cả database của mongo instance được chỉ định.
    - `mongodump --dbpath DB_PATH --out BACKUP_DIRECTORY` : backup database cụ thể tới đường dẫn cụ thể.
    - `mongodump --collection COLLECTION --db DB_NAME` : backup collection cụ thể của database
### **3.2) Restore DB**
- Để restore dữ liệu backup, sử dụng lệnh mongostore. Lệnh này sẽ restore toàn bộ dữ liệu từ thư mục backup.
- Cú pháp :
    ```
    # mongostore
    ```
    - Mặc định, lệnh sẽ đọc data từ thư mục dump trong thư mục hiện hành. Nếu lưu data trong thư mục khác, cần thêm option `--dir=<path_to_backup_data>`
