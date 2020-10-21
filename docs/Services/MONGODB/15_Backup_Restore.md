# Backup và Restore trong MongoDB
## **1) Backup DB**
- Để tạo backup cho database trong **MongoDB**, nên sử dụng lệnh `mongodump`. Lệnh này sẽ dump toàn bộ dữ liệu của server vào một thư mục được chỉ định sẵn. 
- Có nhiều tùy chọn sẵn có giúp giới hạn lượng dữ liệu cần backup .
- Cú pháp :
    ```
    > mongodump
    ```
    - Lệnh này sẽ phả


The command will connect to the server running at 127.0.0.1 and port 27017 and back all data of the server to directory /bin/dump/. Following is the output of the command −