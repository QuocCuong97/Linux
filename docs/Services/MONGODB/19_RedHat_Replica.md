# Quy trình cài đặt MongoDB trên RedHat 7 (replication)
## **Mô hình Primary-Secondary-Secondary**

<p align=center><img src=https://i.imgur.com/wY3xp9n.png></p>

## **Các bước cài đặt**
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
            bindIp: localhost,<hostname(s)|ip address(es)>
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
    # mongo
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