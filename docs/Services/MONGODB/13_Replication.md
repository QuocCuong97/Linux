# Replication
## **1) Khái niệm Replication**
- **Replication** là quá trình đồng bộ dữ liệu giữa nhiều server.
- **Replication** cung cấp tính dự phòng và tăng tính sẵn sàng cho dữ liệu bằng việc tạo ra nhiều bản copy của dữ liệu trên các database server khác nhau . **Replication** bảo vệ database khỏi bị mất trên 1 server riêng lẻ. **Replication** cũng cho phép khôi phục dữ liệu từ việc phần cứng bị hỏng hay service bị gián đoạn . Với các dữ liệu được copy thêm, có thể chỉ định cho các mục đích như khôi phục, báo cáo hay backup .
- Lí do cần **replication** :
    - Để dữ data an toàn
    - Tăng tính sẵn sàng dữ liệu (`24/7`)
    - Khôi phục được từ sự cố
    - Không có downtime khi bảo trì (như backup, index rebuild, nén dữ liệu)
    - Read scaling (có nhiều bản copy hơn để đọc)
    - **Replica set** là hoàn toàn trong suốt với các ứng dụng
## **2) Cách làm việc của replication trong MongoDB**
- Một **replica set** trong **MongoDB** là một nhóm các instance chạy **mongod** duy trì cùng một bộ dữ liệu. 
- Các **replica set** cung cấp tính dự phòng và tính sẵn sàng cao và là cơ sở để triển khai nhập xuất dữ liệu khi cần thiết
### **2.1) Mô hình replication 3 node**
- Một **replica set** chứa một số các ***data-bearing node*** và có thể có thêm 1 ***arbiter node***. Trong các ***data-bearing node***, một và chỉ một member sẽ là **primary node**, các node còn lại sẽ là **secondary node** .
- **Primary node** sẽ nhận các thao tác write. Một **replica set** chỉ có thể có 1 primary có khả năng write với cài đặt `{ w: "majority" }`. Các primary record chỉ thay đổi trên các data set của chính nó trong operation log, oplog,...

    <p align=center><img src=https://i.imgur.com/In34Te3.png width=40%></p>

- Các **Secondary node** sẽ replicate oplog của **primary** và apply quá trình đó vào các dataset do đó các data set của các **secondary** sẽ ánh xạ data set của **primary**. Nếu **primary** không khả dụng, một **secondary** xứng đáng sẽ tổ chức bầu chọn và bầu chọn chính nó làm **primary** mới ,

    <p align=center><img src=https://i.imgur.com/MoRiAmK.png width=40%></p>

- Trong một vài trường hợp (ví dụ như có 1 **primary** và 1 **secondary** nhưng không có thêm chi phí cho một **secondary** nữa), có thể chọn cách add thêm moojtn instance chạy `mongod` vào **replica set** như một **arbiter**. Một **arbiter** tham gia vào bình vầu nhưng sẽ không giữ data (hay không cung cấp cơ chế dự phòng dữ liệu)

    <p align=center><img src=https://i.imgur.com/V9i3jBA.png width=40%></p>

### **2.2) Cơ chế bình bầu giữa các node**
- **Replica set** sử dụng việc bình bầu để quyết định member nào sẽ là primary. **Replica set** thực hiện bình bầu primary khi xảy ra các event sau :
    - Thêm một node mới vào **replica set**
    - Khởi tạo một **replica set**
    - Thực hiện maintainance **replica set** bằng cách sử dụng phương thức `rs.stepDown()` hoặc `rs.reconfig()`, và các **secondary** sẽ mất kết nối tới **primary** lâu hơn thời gian timeout được cấu hình (mặc định `10s`)
- **Replica set** không thể thực hiện write dữ liệu cho tới khi việc bình bầu thành công. **Replica set** chỉ thực hiện được các câu truy vấn khi các **secondary** cũng có khả năng thực hiện được .
- Cơ chế bình bầu này cũng được gọi là cơ chế **Automatic Failover** :

    <p align=center><img src=https://i.imgur.com/W06mjKc.png width=40%></p>

#### **Các yếu tố ảnh hưởng đến việc bình bầu **primary****
- Giao thức **Replication Election Protocol** :
    - Từ version `4.0`, **MongoDB** chỉ hỗ trợ **replica set protocol version 1 `(pv1)`**
    - Tham khảo : [Replica Set Protocol Version](https://docs.mongodb.com/manual/reference/replica-set-protocol-versions/)
- **Heartbeat** :
    - Các member của **replica set** gửi các heartbeat (ping) tới nhau mỗi `2s`. Nếu heartbeat không phản hồi trong `10s`, các member khác sẽ đánh dấu member quá hạn này là *không thể truy cập được (inaccessible)*
- **Member Priority** :
    - Sau khi **replica set** chọn ra được 1 **primary**, thuật toán bầu chọn sẽ chọn những **secondary** có priority lớn nhất ở cuộc bầu chọn. **Member priority** ảnh hưởng đến cả thời gian và kết quả của cuộc bầu chọn. Các **secondary** có **priority** cao hơn sẽ tham gia bầu chọn trước, và có cơ hội thắng cao hơn. Tuy nhiên, cũng có trường hợp **priority** thấp hơn được chọn. Các member sẽ tiếp tục bầu cử cho đến khi chọn được thành viên tốt nhất .
    - Member có **priority** là `0` sẽ không thể trở thành **primary** và cũng không thể tham gia bầu cử
    - Tham khảo : [Priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/)
- **Mirrored Reads** :
    - Bắt đầu từ phiên bản `4.4`, **MongoDB** cung cấp cơ chế **mirror reads** để trước vào bộ nhớ cache của các **secondary** với dữ liệu được truy cập gần đây nhất . Với **mirror reads**, **primary** có thể ánh xạ tập hợp các thao tác mà nó nhận được đến và gửi chúng đến các **secondary**. Việc ánh xạ cache như vậy cũng giúp hiệu suất được phục hồi nhanh hơn sau khi việc bầu chọn diễn ra .
    - Tham khảo : [Mirror Reads](https://docs.mongodb.com/manual/replication/#mirrored-reads)
- **Mất Data Center** :
    - Với các **replica set** được phân tán, việc mất kết nối 1 data center cũng có thể ảnh hưởng đến khả năng của các member còn lại trong các data center khác khi bầu chọn **primary** .
    - Nếu có thể, hãy phân phối các member của **replica set** trên nhiều data center để tối đa hóa khả năng ngay cả khi mất một data center, các member khác cũng có thể bầu chọn lại **primary** .
    - Tham khảo : [Phân tán replica set trên 1 hoặc nhiều Datacenter](https://docs.mongodb.com/manual/core/replica-set-architecture-geographically-distributed/)
- **Network partition** :
    - Việc chia mạng có thể tách một **primary** vào phân vùng mạng mà ít thấy được các node khác . Khi **primary** phát hiện ra nó chỉ có thể giao tiếp với một số ít các node trong **replica set**, nó sẽ tự động bước xuống và trở thành **secondary** . Một member khác, có thể giao tiếp với nhiều node nhất trong **replica set** sẽ độc lập đứng lên tổ chức bầu chọn lại để trở thành **primary** .
#### **Các member được tham gia quá trình bầu chọn**
- Phần cấu hình `members[n].votes` của member trong **replica set**  và member state sẽ quyết định xem member có được ở trong cuộc bầu chọn không .
- Tất cả các member có `members[n].votes` sẽ được tham gia bầu chọn. Để loại trừ một member không cho tham gia bầu chọn, đổi giá trị này thành `0` .
    - Các member không được vote (`votes = 0`) phải có priority là `0`
    - Member có priority > `0` không thể đặt `votes = 0`
- Chỉ có các member được quyền vote ở đang trong các trạng thái (state) sau thì mới được tham gia vote :
    - `PRIMARY`
    - `SECONDARY`
    - `STARTUP2`
    - `RECOVERING`
    - `ARBITER`
    - `ROLLBACK`
#### **Các member không được tham gia bầu chọn**
- Mặc dù các member không có quyền sẽ không được bầu chọn, các member này vẫn giữ bản copy dữ liệu của **replica set** và có thể chấp nhận thao tác đọc từ các ứng dụng của client .
- Bởi vì **replica set** có tới `50` member, nhưng chỉ có `7` member được tham gia vote, non-voting member sẽ cho phép **replica set** có nhiều hơn 7 member .
- **VD :** Có `9` member trong **replica set** thì sẽ có `7` member được vote và 2 member không được vote :

    <p align=center><img src=https://i.imgur.com/3G1TFzr.png width=40%></p>

    - Non-vote member sẽ có cả `votes=0` và `priority=0` như sau :
        ```json
        {
            "_id" : <num>,
            "host" : <hostname:port>,
            "arbiterOnly" : false,
            "buildIndexes" : true,
            "hidden" : false,
            "priority" : 0,
            "tags" : {

            },
            "slaveDelay" : NumberLong(0),
            "votes" : 0
        }
        ```
## **3) Các kiến trúc deploy Replica set**
### **3.1) Kiến trúc 3 member**
- Số lượng member tối thiểu của **replica set** để thấy được lợi ích của nó là `3` member. Một **replica set** với `3` member có thể là cả `3` member đều mang dữ liệu (**Primary-Secondary-Secondary** - nên dùng) hoặc trường hợp khác cần tiết kiệm chi phí hơn khi thêm member mang dữ liệu thứ 3, đó là 2 member mang dữ liệu và 1 **arbiter** (**Primary-Secondary-Arbiter**)
#### **3.1.1) Kiến trúc Primary-Secondary-Secondary (P-S-S)**
- Một **replica set** với 3 members lưu trữ data có :
    - 1 **primary**
    - 2 **secondary**. Các **secondary** đều có thể trở thành **primary** sau khi bình chọn .

        <p align=center><img src=https://i.imgur.com/MqYQLGz.png width=40%></p>

- Cách deploy này cung cấp 2 bản copy hoàn chỉnh của data set tại mọi thời điểm trong **primary**. **Replica set** cung cấp khả năng chịu lỗi và tính sẵn sàng. Nếu **primary** không khả dụng, **replica set** sẽ bầu chọn **secondary** và tiếp tục hoạt động . **Primary** cũ sẽ join lại sau khi khả dụng .

    <p align=center><img src=https://i.imgur.com/GF5s77Z.png width=40%></p>

#### **3.1.2) Kiến trúc Primary-Secondary-Arbiter (P-S-A)**
- Một **replica set** với 2 members lưu trữ data có :
    - 1 **primary**
    - 1 **secondary**. **Secondary** đều có thể trở thành **primary** sau khi bình chọn .
    - 1 **arbiter**. **Arbiter** sẽ chỉ vote khi bình chọn .

    <p align=center><img src=https://i.imgur.com/oGPYX16.png width=40%></p>

- Bởi vì **arbiter** không chứa bản copy data, cách deploy này chỉ cung cấp 1 bản copy hoàn chỉnh duy nhất. **Arbiter** yêu cầu ít tài nguyên hơn, nhưng tính dự phòng và khả năng chịu lỗi hạn chế hơn .
- Tuy nhiên, cách deploy này vẫn đảm bảo được tính sẵn sàng nếu **primary** hoặc **secondary** bị chết. Nếu **primary** chết, **replica set** sẽ bầu chọn **secondary** làm **primary** .

    <p align=center><img src=https://i.imgur.com/wXAGR4P.png width=40%></p>

### **3.2) Phân phối Replica Set ở một hoặc hai DataCenter**
## **4) Deploy Replica Set**
### **4.1) Mô hình Primary-Secondary-Secondary**
#### **Mô hình**
<p align=center><img src=https://i.imgur.com/wY3xp9n.png></p>

#### **Cài đặt MongoDB trên cả 3 node**
#### **Cấu hình Replica Set**
- **B1 :** Cấu hình hostname chi cả 3 node :
    ```
    # hostnamectl set-hostname [mongodb_1|mongodb_2|mongodb_3]
    ```
- **B2 :** Khai báo file `/etc/hosts` trên cả 3 node :
    ```
    # echo "10.5.10.151 mongodb_1" >> /etc/hosts
    # echo "10.5.10.153 mongodb_2" >> /etc/hosts
    # echo "10.5.10.161 mongodb_3" >> /etc/hosts
    ```
- **B3 :** Khai báo tên **replica set** và **bind IP** trên cả 3 node :
    ```
    # vi /etc/mongod.conf
    ```
    - Chỉnh sửa nội dung sau:
        ```yaml
        ...
        net:
            bindIp: localhost,<IP_node>
        ...
        replication:
            replSetName: "rs0"
        ...
        ```
- **B4 :** Khởi động lại dịch vụ `mongod` :
    ```
    # systemctl restart mongod
    ```
- **B5 :** Trên một node bất kỳ, khởi tạo **replica set**:
    ```
    # mongo --host rs0/localhost:27017
    > rs.initiate()
    {
            "info2" : "no configuration specified. Using a default configuration for the set",
            "me" : "10.5.10.151:27017",
            "ok" : 1,
            "$clusterTime" : {
                    "clusterTime" : Timestamp(1602729545, 1),
                    "signature" : {
                            "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                            "keyId" : NumberLong(0)
                    }
            },
            "operationTime" : Timestamp(1602729545, 1)
    }
    rs0:SECONDARY>
    ```
- **B6 :** Add các member vào **replica set** :
    ```
    rs0:SECONDARY> rs.add('mongodb_2:27017')
    {
            "ok" : 1,
            "$clusterTime" : {
                    "clusterTime" : Timestamp(1602729787, 1),
                    "signature" : {
                            "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                            "keyId" : NumberLong(0)
                    }
            },
            "operationTime" : Timestamp(1602729787, 1)
    }
    rs0:PRIMARY>
    ```
    ```
    rs0:PRIMARY> rs.add('mongodb_3:27017')
    {
            "ok" : 1,
            "$clusterTime" : {
                    "clusterTime" : Timestamp(1602729838, 1),
                    "signature" : {
                            "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                            "keyId" : NumberLong(0)
                    }
            },
            "operationTime" : Timestamp(1602729838, 1)
    }
    rs0:PRIMARY>
    ```
- **B7 :** Kiểm tra trạng thái của **replica set** :
    ```
    rs0:PRIMARY> rs.status()
    {
            "set" : "rs0",
            "date" : ISODate("2020-10-15T02:45:42.686Z"),
            "myState" : 1,
            "term" : NumberLong(1),
            "syncSourceHost" : "",
            "syncSourceId" : -1,
            "heartbeatIntervalMillis" : NumberLong(2000),
            "majorityVoteCount" : 2,
            "writeMajorityCount" : 2,
            "votingMembersCount" : 3,
            "writableVotingMembersCount" : 3,
            "optimes" : {
                    "lastCommittedOpTime" : {
                            "ts" : Timestamp(1602729935, 1),
                            "t" : NumberLong(1)
                    },
                    "lastCommittedWallTime" : ISODate("2020-10-15T02:45:35.812Z"),
                    "readConcernMajorityOpTime" : {
                            "ts" : Timestamp(1602729935, 1),
                            "t" : NumberLong(1)
                    },
                    "readConcernMajorityWallTime" : ISODate("2020-10-15T02:45:35.812Z"),
                    "appliedOpTime" : {
                            "ts" : Timestamp(1602729935, 1),
                            "t" : NumberLong(1)
                    },
                    "durableOpTime" : {
                            "ts" : Timestamp(1602729935, 1),
                            "t" : NumberLong(1)
                    },
                    "lastAppliedWallTime" : ISODate("2020-10-15T02:45:35.812Z"),
                    "lastDurableWallTime" : ISODate("2020-10-15T02:45:35.812Z")
            },
            "lastStableRecoveryTimestamp" : Timestamp(1602729905, 1),
            "electionCandidateMetrics" : {
                    "lastElectionReason" : "electionTimeout",
                    "lastElectionDate" : ISODate("2020-10-15T02:39:05.734Z"),
                    "electionTerm" : NumberLong(1),
                    "lastCommittedOpTimeAtElection" : {
                            "ts" : Timestamp(0, 0),
                            "t" : NumberLong(-1)
                    },
                    "lastSeenOpTimeAtElection" : {
                            "ts" : Timestamp(1602729545, 1),
                            "t" : NumberLong(-1)
                    },
                    "numVotesNeeded" : 1,
                    "priorityAtElection" : 1,
                    "electionTimeoutMillis" : NumberLong(10000),
                    "newTermStartDate" : ISODate("2020-10-15T02:39:05.767Z"),
                    "wMajorityWriteAvailabilityDate" : ISODate("2020-10-15T02:39:05.809Z")
            },
            "members" : [
                    {
                            "_id" : 0,
                            "name" : "10.5.10.151:27017",
                            "health" : 1,
                            "state" : 1,
                            "stateStr" : "PRIMARY",
                            "uptime" : 518,
                            "optime" : {
                                    "ts" : Timestamp(1602729935, 1),
                                    "t" : NumberLong(1)
                            },
                            "optimeDate" : ISODate("2020-10-15T02:45:35Z"),
                            "syncSourceHost" : "",
                            "syncSourceId" : -1,
                            "infoMessage" : "",
                            "electionTime" : Timestamp(1602729545, 2),
                            "electionDate" : ISODate("2020-10-15T02:39:05Z"),
                            "configVersion" : 3,
                            "configTerm" : 1,
                            "self" : true,
                            "lastHeartbeatMessage" : ""
                    },
                    {
                            "_id" : 1,
                            "name" : "mongodb_2:27017",
                            "health" : 1,
                            "state" : 2,
                            "stateStr" : "SECONDARY",
                            "uptime" : 154,
                            "optime" : {
                                    "ts" : Timestamp(1602729935, 1),
                                    "t" : NumberLong(1)
                            },
                            "optimeDurable" : {
                                    "ts" : Timestamp(1602729935, 1),
                                    "t" : NumberLong(1)
                            },
                            "optimeDate" : ISODate("2020-10-15T02:45:35Z"),
                            "optimeDurableDate" : ISODate("2020-10-15T02:45:35Z"),
                            "lastHeartbeat" : ISODate("2020-10-15T02:45:42.313Z"),
                            "lastHeartbeatRecv" : ISODate("2020-10-15T02:45:42.313Z"),
                            "pingMs" : NumberLong(0),
                            "lastHeartbeatMessage" : "",
                            "syncSourceHost" : "10.5.10.151:27017",
                            "syncSourceId" : 0,
                            "infoMessage" : "",
                            "configVersion" : 3,
                            "configTerm" : 1
                    },
                    {
                            "_id" : 2,
                            "name" : "mongodb_3:27017",
                            "health" : 1,
                            "state" : 2,
                            "stateStr" : "SECONDARY",
                            "uptime" : 104,
                            "optime" : {
                                    "ts" : Timestamp(1602729935, 1),
                                    "t" : NumberLong(1)
                            },
                            "optimeDurable" : {
                                    "ts" : Timestamp(1602729935, 1),
                                    "t" : NumberLong(1)
                            },
                            "optimeDate" : ISODate("2020-10-15T02:45:35Z"),
                            "optimeDurableDate" : ISODate("2020-10-15T02:45:35Z"),
                            "lastHeartbeat" : ISODate("2020-10-15T02:45:42.325Z"),
                            "lastHeartbeatRecv" : ISODate("2020-10-15T02:45:42.497Z"),
                            "pingMs" : NumberLong(0),
                            "lastHeartbeatMessage" : "",
                            "syncSourceHost" : "mongodb_2:27017",
                            "syncSourceId" : 1,
                            "infoMessage" : "",
                            "configVersion" : 3,
                            "configTerm" : 1
                    }
            ],
            "ok" : 1,
            "$clusterTime" : {
                    "clusterTime" : Timestamp(1602729935, 1),
                    "signature" : {
                            "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                            "keyId" : NumberLong(0)
                    }
            },
            "operationTime" : Timestamp(1602729935, 1)
    }
- **B8 :** Kiểm tra các cấu hình của **replica set** :
    ```
    rs0:PRIMARY> rs.conf()
    {
            "_id" : "rs0",
            "version" : 3,
            "term" : 1,
            "protocolVersion" : NumberLong(1),
            "writeConcernMajorityJournalDefault" : true,
            "members" : [
                    {
                            "_id" : 0,
                            "host" : "10.5.10.151:27017",
                            "arbiterOnly" : false,
                            "buildIndexes" : true,
                            "hidden" : false,
                            "priority" : 1,
                            "tags" : {

                            },
                            "slaveDelay" : NumberLong(0),
                            "votes" : 1
                    },
                    {
                            "_id" : 1,
                            "host" : "mongodb_2:27017",
                            "arbiterOnly" : false,
                            "buildIndexes" : true,
                            "hidden" : false,
                            "priority" : 1,
                            "tags" : {

                            },
                            "slaveDelay" : NumberLong(0),
                            "votes" : 1
                    },
                    {
                            "_id" : 2,
                            "host" : "mongodb_3:27017",
                            "arbiterOnly" : false,
                            "buildIndexes" : true,
                            "hidden" : false,
                            "priority" : 1,
                            "tags" : {

                            },
                            "slaveDelay" : NumberLong(0),
                            "votes" : 1
                    }
            ],
            "settings" : {
                    "chainingAllowed" : true,
                    "heartbeatIntervalMillis" : 2000,
                    "heartbeatTimeoutSecs" : 10,
                    "electionTimeoutMillis" : 10000,
                    "catchUpTimeoutMillis" : -1,
                    "catchUpTakeoverDelayMillis" : 30000,
                    "getLastErrorModes" : {

                    },
                    "getLastErrorDefaults" : {
                            "w" : 1,
                            "wtimeout" : 0
                    },
                    "replicaSetId" : ObjectId("5f87b64957ab1886d4ee13b6")
            }
    }
    ```
- **B9 :** Thử tạo document trên node **primary** và kiểm tra kết quả trên các node khác :
    ```
    rs0:PRIMARY> use mydb
    switched to db mydb
    rs0:PRIMARY> db.movie.insert({"name": "The Avengers", "year": 2012})
    WriteResult({ "nInserted" : 1 })
    ```
### **4.2) Mô hình Primary-Secondary-Arbiter**
#### **Mô hình**
<img src=https://i.imgur.com/ghHY0hD.png>

- Thực hiện tương tự các bước như mô hình **P-S-S** cho đến bước thêm member .
- Thêm member trên primary :
    ```shell
    rs0:PRIMARY> rs.add("10.5.10.153:27017")
    rs0:PRIMARY> rs.addArb("10.5.10.161:27017")
    ```
## **5) Các command thường dùng với Replication**
### **5.1) Các lệnh kết nối database**
- Kết nối vào **replica** :
    ```
    # mongo --host <replica_name>/<replica_member>:<port>
    ```
    - **VD :**
        ```
        # mongo --host rs0/10.5.10.151:27017
        ```
### **5.2) Mongo shell**
- Khởi tạo **replica set** :
    ```shell
    > rs.initiate()
    ```
- Thêm member vào **replica** :
    ```shell
    > rs.add("host:port")
    ```
- Thêm **Arbiter** vào **replica** :
    ```shell
    > rs.addArb("host:port")
    ```
- Xóa member khỏi **replica** :
    ```shell
    > rs.remove("host:port")
    ```
- Xem thông tin **primary** và **secondary** trong **replica** :
    ```shell
    > rs.status()
    ```
- Xem thông tin cấu hình của **replica** :
    ```shell
    > rs.conf()
    ```
- Kiểm tra nhanh thông tin master của db :
    ```shell
    > db.isMaster()
    ```
- Thay thế member :
    ```shell
    > cfg = rs.conf()
    > cfg.members[0].host = "<new_host>"
    > rs.reconfig(cfg)
    ```
    > `[0]` là thứ tự của host trong `status()` hoặc `conf()`. Có thể thay đổi
- Thay đổi priority :
    ```shell
    > cfg = rs.conf()
    > cfg.members[0].priority = <new_value>
    > rs.reconfig(cfg)
    ```
    > `[0]` là thứ tự của host trong `status()` hoặc `conf()`. Có thể thay đổi
## **6) Connection String URI Format**
### **6.1) Standard Connection String Format**
- Định dạng connection URI này được sử dụng để connect đến các mô hình : **standalone**, **replica set** hoặc **sharded cluster** .
- Định dạng URI sẽ có cấu trúc sau :
    ```
    mongodb://[username:password@]host1[:port1][,...hostN[:portN]][/[defaultauthdb][?options]]
    ```
    - Trong đó :
        - `mongodb://` : phần prefix bắt buộc để nhận dạng standard connection
        - `username:password@` : thông tin xác thực (không bắt buộc)
            - Nếu được chỉ định, client sẽ xác thực user trên `authSource`. Nếu `authSource` không được chỉ định, client sẽ xác thực trên `defaultauthdb`. Nếu `defaultauthdb` không được chỉ định, sẽ xác thực trên database `admin`
            - Nếu username hoặc password có chứa các ký tự `@`, `:`, `/`, `%`, sử dụng [percent encoding](https://tools.ietf.org/html/rfc3986#section-2.1)
        - `host[:port]` : Host (và tùy chọn port) mà `mongod` instance (hoặc `mongos` instance cho cluster) đang chạy. Có thể chỉ định hostname, IP hoặc UNIX domain socket. Nếu không chỉ định `port`, mặc định sử dụng `27017` .
        - `/defaultauthdb` : tên database xác thực
        - `?<options>` : [Tham khảo thêm](https://docs.mongodb.com/manual/reference/connection-string/#connections-connection-options)
- **VD :**
    - Kết nối **Standalone** :
        ```
        mongodb://mongodb0.example.com:27017
        ```
        hoặc
        ```
        mongodb://myDBReader:Password123@mongodb0.example.com:27017/?authSource=admin
        ```
    - Kết nối **Replica Set** : kết nối `mongod` instance trong **Replica Set** :
        ```
        mongodb://mongodb0.example.com:27017,mongodb1.example.com:27017,mongodb2.example.com:27017/?replicaSet=myRepl
        ```
        hoặc
        ```
        mongodb://myDBReader:Password123@mongodb0.example.com:27017,mongodb1.example.com:27017,mongodb2.example.com:27017/?authSource=admin&replicaSet=myRepl
        ```
    - Kết nối **Sharded Cluster** : kết nối `mongos` instance :
        ```
        mongodb://mongos0.example.com:27017,mongos1.example.com:27017,mongos2.example.com:27017
        ```
        hoặc
        ```
        mongodb://myDBReader:Password123@mongos0.example.com:27017,mongos1.example.com:27017,mongos2.example.com:27017/?authSource=admin
        ```
- Cách kết nối **Mongo Shell** sử dụng connection string URI :
    ```
    mongo <mongo_connection_URI>
    ```
    - **VD :**
        ```
        mongo mongodb://mongodb0.example.com:27017
        ```
### **6.2) DNS Seed List Connection Format**
- [Tham khảo thêm](https://docs.mongodb.com/manual/reference/connection-string/#dns-seed-list-connection-format)
- 