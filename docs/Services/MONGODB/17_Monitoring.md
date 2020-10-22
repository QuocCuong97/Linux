# Monitoring trong MongoDB
## **1) Công cụ `mongostat`**
- Công cụ `mongostat` cung cấp cái nhìn nhanh về trạng thái hiện tại của `mongod` hoặc `mongos` instance .
- `mongostat` hoạt động tương tự như công cụ `vmstat`, nhưng chỉ cung cấp câc thông tin về `mongod` hoặc `mongos` instance.
- Chạy lệnh `mongostat` trong commandline, không phải trong mongo shell.
- Cú pháp :
    ```
    # mongostat <options> <connection-string> <polling interval in seconds>
    ```
    - **Options** :
        - `--version` : hiển thị phiên bản của `mongostat`
        - `--uri=<connectionString>` : uri kết nối đến mongo instance
        - `--host=<hostname><:port>, -h=<hostname><:port>` : chỉ định host muốn monitor
        - `--username=<username>, -u=<username>` : chỉ định user xác thực
        - `--password=<password>, -p=<password>` : chỉ định password của user xác thực
        - `--humanReadable=True` : hiển thị ở định dạng dễ đọc
        - `-o=<field list>` : chỉ định các cột sẽ hiển thị ra output
        - `--noheaders` : không hiển thị tên cột
        - `--rowcount=<number>, -n=<number>` : giới hạn số dòng trả ra trong output
        - `--json` : trả về kết quả dạng json
    - `<sleeptime>` : mặc định là `1s` . chu kỳ trả về kết quả hoạt động của **mongodb**
- Các field trong output của `mongostat` :
    - `insert` : số object insert vào database trong mỗi second. Nếu đi kèm theo dấu `*` có nghĩa là liên quan đến quá trình **replication**
    - `query` : số thao tác query trong mỗi giây
    - `update` : số thao tác update trong mỗi giây
    - `delete` : số thao tác xóa tring mỗi giây
    - `getmore` : số thao tác getmore trong mỗi second
    - `command` : số command trên mỗi second . `mongostat` sử dụng pipeline theo cấu trúc `local|replicated` command
    - `flushes`
    - `dirty`
    - `used`
    - `vsize` : lượng virtual memory theo megabytes được sử dụng bởi tiến trình tại thời điểm trích xuất dữ liệu
    - `res` : lượng resident memory theo megabytes được sử dụng bởi tiến trình tại thời điểm trích xuất dữ liệu
    - `locked` : 
    - `qr` : lượng client đang queue để read
    - `qw` : lượng client đang queue để write
    - `ar` : số active client thực hiện tác vụ read
    - `aw` : số active client thực hiện tác vụ write
    - `netIn` : lượng traffic được nhận vào bởi mongo instance tính theo `bytes` (tính cả lệnh `mongostat`)
    - `netOut` : lượng traffic được gửi đi bởi mongo instance tính theo `bytes` (tính cả lệnh `mongostat`)
    - `conn` : tổng số các kết nối đang mở
    - `set` : tên của **replica set**
    - `repl` : trạng thái replication của member
- **VD :**
    ```
    # mongostat
    insert query update delete getmore command dirty  used flushes vsize  res qrw arw net_in net_out conn                time
        *0    *0     *0     *0       0     0|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   110b   41.5k    3 Oct 19 21:06:07.607
        *0    *0     *0     *0       0     0|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   111b   41.8k    3 Oct 19 21:06:08.613
        *0    *0     *0     *0       0     0|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   110b   41.4k    3 Oct 19 21:06:09.628
        *0    *0     *0     *0       0     1|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   114b   43.2k    3 Oct 19 21:06:10.603
        *0    *0     *0     *0       0     1|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   112b   42.2k    3 Oct 19 21:06:11.601
        *0    *0     *0     *0       0     1|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   112b   42.4k    3 Oct 19 21:06:12.594
        *0    *0     *0     *0       0     0|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   111b   41.8k    3 Oct 19 21:06:13.602
        *0    *0     *0     *0       0     1|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   112b   42.1k    3 Oct 19 21:06:14.601
        *0    *0     *0     *0       0     0|0  0.0% 19.5%       1 1.72G 315M 0|0 1|0   111b   41.8k    3 Oct 19 21:06:15.607
        *0    *0     *0     *0       0     2|0  0.0% 19.5%       0 1.72G 315M 0|0 1|0   307b   42.7k    3 Oct 19 21:06:16.608
    ```
## **2) Công cụ `mongotop`**
- `mongotop` cung cấp phương thức để theo dõi khoảng thời gian mongod instance dùng để đọc, ghi dữ liệu
- `mongotop` cung cấp dữ liệu thống kê mức collection
- Mặc định, `mongotop` trả về dữ liệu theo giây
- Cú pháp :
    ```
    # mongotop [options] [frequency]
    ```
    - **Options** :
        - `--version` : hiển thị phiên bản của `mongotop`
        - `--uri=<connectionString>` : uri kết nối đến mongo instance
        - `--host=<hostname><:port>, -h=<hostname><:port>` : chỉ định host muốn monitor
        - `--username=<username>, -u=<username>` : chỉ định user xác thực
        - `--password=<password>, -p=<password>` : chỉ định password của user xác thực
        - `--rowcount=<int>, -n=<int>` : giới hạn số dòng trả ra trong output
        - `--json` : trả về kết quả dạng json
    - `<sleeptime>` : mặc định là `1s` . chu kỳ trả về kết quả hoạt động của **mongodb**
- **VD :**
    ```
    # mongotop
    2020-10-19T22:30:13.843+0700    connected to: mongodb://localhost/

                          ns    total    read    write    2020-10-19T22:30:14+07:00
        admin.$cmd.aggregate      0ms     0ms      0ms
          admin.system.roles      0ms     0ms      0ms
        admin.system.version      0ms     0ms      0ms
      config.system.sessions      0ms     0ms      0ms
         config.transactions      0ms     0ms      0ms
              local.oplog.rs      0ms     0ms      0ms
        local.system.replset      0ms     0ms      0ms
                  mydb.movie      0ms     0ms      0ms
           mydb.mycollection      0ms     0ms      0ms
                 mydb.mycols      0ms     0ms      0ms

                          ns    total    read    write    2020-10-19T22:30:15+07:00
        admin.$cmd.aggregate      0ms     0ms      0ms
          admin.system.roles      0ms     0ms      0ms
        admin.system.version      0ms     0ms      0ms
      config.system.sessions      0ms     0ms      0ms
         config.transactions      0ms     0ms      0ms
              local.oplog.rs      0ms     0ms      0ms
        local.system.replset      0ms     0ms      0ms
                  mydb.movie      0ms     0ms      0ms
           mydb.mycollection      0ms     0ms      0ms
                 mydb.mycols      0ms     0ms      0ms
    ```
## **3) Lệnh `db.serverStatus()`**
## **4) Cloud-based Monitoring**
- Kể từ ver `4.0`, **MongoDB** (Community Edition) cung cấp tính năng **Cloud monitoring** miễn phí cho **standalone** và các **replica set**
- Các thông tin có thể monitor :
    - Thời gian thực hiện thao tác (Operation Execution Times)
    - Lượng RAM sử dụng (Memory Usage)
    - Lượng CPU sử dụng (CPU Usage)
    - Đếm số lượng các thao tác (Operation Counts)
- Khi được bật, dữ liệu được monitor sẽ được upload theo khoảng thời gian `1p`. Dữ liệu monitor sẽ hết hạn sau `24h`.
- Để kích hoạt monitor sử dụng lệnh :
    ```
    > db.enableFreeMonitoring()
    ```
- Để hủy kích hoạt monitor sử dụng lệnh :
    ```
    > db.disableFreeMonitoring()
    ```
- **VD1 :** Monitor Standalone :
    ```
    # mongo
    MongoDB shell version v4.4.1
    connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
    Implicit session: session { "id" : UUID("3746efc1-be67-440e-b874-c4341cc666c7") }
    MongoDB server version: 4.4.1
    Welcome to the MongoDB shell.
    For interactive help, type "help".
    For more comprehensive documentation, see
            https://docs.mongodb.com/
    Questions? Try the MongoDB Developer Community Forums
            https://community.mongodb.com
    ---
    The server generated these startup warnings when booting:
            2020-10-22T01:17:14.210+07:00: ***** SERVER RESTARTED *****
            2020-10-22T01:17:16.515+07:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
            2020-10-22T01:17:16.515+07:00: /sys/kernel/mm/transparent_hugepage/enabled is 'always'. We suggest setting it to 'never'
            2020-10-22T01:17:16.515+07:00: /sys/kernel/mm/transparent_hugepage/defrag is 'always'. We suggest setting it to 'never'
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
    > db.enableFreeMonitoring()
    {
            "state" : "enabled",
            "message" : "To see your monitoring data, navigate to the unique URL below. Anyone you share the URL with will also be able to view this page. You can disable monitoring at any time by running db.disableFreeMonitoring().",
            "url" : "https://cloud.mongodb.com/freemonitoring/cluster/RU3ZH6OLMTZTEDJUC5OVHWHNNSXRAQN6",
            "userReminder" : "",
            "ok" : 1
    }
    ```
    - Truy cập URL được trả về :
        ```
        https://cloud.mongodb.com/freemonitoring/cluster/RU3ZH6OLMTZTEDJUC5OVHWHNNSXRAQN6
        ```
    - Kết quả :

        <img src=https://i.imgur.com/cUEUyJy.png>

        <img src=https://i.imgur.com/jcoVEa1.png>
- **VD2 :** Monitor **Replica Set** :

    <img src=https://i.imgur.com/zS2qY5H.png>

## **5) Công cụ Mongo Compass**
------------------------
**Tham khảo**
- [`mongostat`](https://docs.mongodb.com/database-tools/mongostat/)
- [`mongotop`](https://docs.mongodb.com/database-tools/mongotop/)
- [Cloud-based Monitoring](https://docs.mongodb.com/manual/administration/free-monitoring/)
- [Monitor MongoDB](https://docs.mongodb.com/manual/administration/monitoring/)