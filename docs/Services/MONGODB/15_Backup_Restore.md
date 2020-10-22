# Backup và Restore trong MongoDB
## **1) Backup DB**
- Để tạo backup cho database trong **MongoDB**, nên sử dụng lệnh `mongodump`. Lệnh này sẽ dump toàn bộ dữ liệu của server vào một thư mục được chỉ định sẵn. 
- Cú pháp :
    ```
    # mongodump
    ```
    - Lệnh này sẽ kết nối server local (`127.0.0.1:27017`) và lưu tất cả data của server vào thư mục `dump/` trong thư mục hiện hành
- Có nhiều tùy chọn sẵn có giúp giới hạn lượng dữ liệu cần backup .
    - `mongodump --host HOST_NAME --port PORT_NUMBER` : backup tất cả database của mongo instance được chỉ định.
    - `mongodump --dbpath DB_PATH --out BACKUP_DIRECTORY` : backup database cụ thể tới đường dẫn cụ thể
    - `mongodump --collection COLLECTION --db DB_NAME` : backup collection cụ thể của database
- **VD1 :** Backup mặc định
    - Kiểm tra các database trong local server :
        ```
        > show dbs
        admin   0.000GB
        config  0.000GB
        local   0.000GB
        mydb    0.038GB
        ```
    - Thực hiện dump toàn bộ dữ liệu :
        ```
        # mongodump
        2020-10-19T20:18:22.656+0700    writing admin.system.version to dump/admin/system.version.bson
        2020-10-19T20:18:22.667+0700    done dumping admin.system.version (1 document)
        2020-10-19T20:18:22.669+0700    writing mydb.users to dump/mydb/users.bson
        2020-10-19T20:18:22.672+0700    writing mydb.mycollection to dump/mydb/mycollection.bson
        2020-10-19T20:18:22.674+0700    writing mydb.movie to dump/mydb/movie.bson
        2020-10-19T20:18:22.675+0700    writing mydb.mycols to dump/mydb/mycols.bson
        2020-10-19T20:18:22.684+0700    done dumping mydb.movie (5 documents)
        2020-10-19T20:18:22.688+0700    done dumping mydb.mycollection (0 documents)
        2020-10-19T20:18:22.756+0700    done dumping mydb.mycols (0 documents)
        2020-10-19T20:18:25.588+0700    [############............]  mydb.users  521814/1000000  (52.2%)
        2020-10-19T20:18:27.350+0700    [########################]  mydb.users  1000000/1000000  (100.0%)
        2020-10-19T20:18:27.350+0700    done dumping mydb.users (1000000 documents)
        ```
    - Dữ liệu được dump tại thư mục `dump/` trong thư mục hiện hành:
        ```
        [root@centos7-01 ~]# ls -l dump/
        total 0
        drwxr-xr-x. 2 root root  69 Oct 19 20:18 admin
        drwxr-xr-x. 2 root root 202 Oct 19 20:18 mydb
        ```
        > Chỉ có database có dữ liệu mới được backup
- **VD2 :** Backup collection cụ thể :
    ```
    # mongodump --collection users --db mydb
    2020-10-19T20:44:28.768+0700    writing mydb.users to dump/mydb/users.bson
    2020-10-19T20:44:31.718+0700    [####################....]  mydb.users  856602/1000000  (85.7%)
    2020-10-19T20:44:32.062+0700    [########################]  mydb.users  1000000/1000000  (100.0%)
    2020-10-19T20:44:32.062+0700    done dumping mydb.users (1000000 documents)
    ```
    - Kiểm tra collection được dump ra :
    ```
    # ls -l dump/mydb/
    total 69232
    -rw-r--r--. 1 root root 70888890 Oct 19 20:44 users.bson
    -rw-r--r--. 1 root root      240 Oct 19 20:44 users.metadata.json
    ```
## **2) Restore**
- Để restore dữ liệu backup, sử dụng lệnh `mongostore`. Lệnh này sẽ restore toàn bộ dữ liệu từ thư mục backup
- Cú pháp :
    ```
    # mongostore
    ```
    - Mặc định, lệnh sẽ đọc data từ thư mục `dump` trong thư mục hiện hành. Nếu lưu data trong thư mục khác, cần thêm option `--dir=<path_to_backup_data>`
- **VD :**
    - Trước hết, thực hiện xóa database `mydb` :
        ```
        > use mydb
        > db.dropDatabase()
        { "dropped" : "mydb", "ok" : 1 }
        ```
    - Restore lại dữ liệu :
        ```
        # mongorestore --dir=dumps/
        2020-10-19T20:58:15.607+0700    preparing collections to restore from
        2020-10-19T20:58:15.609+0700    reading metadata for mydb.users from dumps/mydb/users.metadata.json
        2020-10-19T20:58:15.610+0700    reading metadata for mydb.movie from dumps/mydb/movie.metadata.json
        2020-10-19T20:58:15.611+0700    reading metadata for mydb.mycollection from dumps/mydb/mycollection.metadata.json
        2020-10-19T20:58:15.619+0700    reading metadata for mydb.mycols from dumps/mydb/mycols.metadata.json
        2020-10-19T20:58:15.645+0700    restoring mydb.movie from dumps/mydb/movie.bson
        2020-10-19T20:58:15.648+0700    restoring mydb.mycollection from dumps/mydb/mycollection.bson
        2020-10-19T20:58:15.660+0700    restoring mydb.users from dumps/mydb/users.bson
        2020-10-19T20:58:15.660+0700    restoring mydb.mycols from dumps/mydb/mycols.bson
        2020-10-19T20:58:15.661+0700    no indexes to restore
        2020-10-19T20:58:15.661+0700    finished restoring mydb.mycollection (0 documents, 0 failures)
        2020-10-19T20:58:15.663+0700    restoring indexes for collection mydb.movie from metadata
        2020-10-19T20:58:15.664+0700    no indexes to restore
        2020-10-19T20:58:15.664+0700    finished restoring mydb.mycols (0 documents, 0 failures)
        2020-10-19T20:58:15.695+0700    finished restoring mydb.movie (5 documents, 0 failures)
        2020-10-19T20:58:18.608+0700    [######..................]  mydb.users  17.7MB/67.6MB  (26.2%)
        2020-10-19T20:58:21.606+0700    [############............]  mydb.users  36.0MB/67.6MB  (53.2%)
        2020-10-19T20:58:24.607+0700    [###################.....]  mydb.users  53.6MB/67.6MB  (79.3%)
        2020-10-19T20:58:26.936+0700    [########################]  mydb.users  67.6MB/67.6MB  (100.0%)
        2020-10-19T20:58:26.936+0700    restoring indexes for collection mydb.users from metadata
        2020-10-19T20:58:29.567+0700    finished restoring mydb.users (1000000 documents, 0 failures)
        2020-10-19T20:58:29.567+0700    1000005 document(s) restored successfully. 0 document(s) failed to restore.
        ```
    - Kiểm tra lại database :
        ```
        > use mydb
        > show collections
        movie
        mycollection
        mycols
        users
        ```