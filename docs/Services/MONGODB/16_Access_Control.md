# Kiểm soát truy cập trong MongoDB
- **MongoDB** sử dụng **Role-Based Access Control (RBAC)** để kiểm soát truy cập trong **MongoDB**. Một user được gán 1 hoặc nhiều **role** để quyết định quyền truy cập của database và các thao tác. Ngoài quyền hạn, user không có quyền truy cập hệ thống
- Mặc định, **mongoDB** không kích hoạt access control. Để kích hoạt, cần cấu hình tùy chọn `security.authorization` trong file `/etc/mongod.conf` . Khi kích hoạt access control, user sẽ phải xác thực mỗi khi truy cập mongo .

## **1) Quản lý Roles**
- Một role sẽ gán đặc quyền được thực thi hành động (*action*) được chỉ định trên *resource* cụ thể . Mỗi đặc quyền được chỉ định rõ ràng trong **role** hoặc thừa kế từ **role** khác hoặc cả hai .
### **1.1) Built-in role**
#### **1.1.1) Các role cho user**
- `read` : cung cấp khả năng đọc dữ liệu trên các non-system collection và collection `system.js`
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#read)
- `readWrite` : cung cấp tất cả các đặc quyền của `read` cộng thêm khả năng chỉnh sửa dữ liệu trên các non-system collection và collection `system.js` .
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#readWrite)
#### **1.1.2) Các role cho admin**
- `dbAdmin` : cung cấp khả năng thực hiện các tác vụ quản lý như schema-related tasks, indexing hay thu thập thống kê . **Role** này không được gán cho user .
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#dbAdmin)
- `dbOwner` : người sở hữu database có thể thực hiện các tác vụ quản trị database. **Role** này kết hợp các quyền được gán bởi `readWrite`, `dbAdmin` và `userAdmin`
- `userAdmin` : cung cấp khả năng tạo và chỉnh sửa **role** và **user** trên database hiện hành.
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#userAdmin)  
#### **1.1.3) Các role trên Cluster**
- `clusterAdmin` : cung cấp khả năng quản lý cluster tốt nhất. **Role** này kết hợp đặc quyền được gán bởi `clusterManager`, `clusterMonitor`, và `hostManager`. Thêm vào đó, **role** cũng cho phép xóa database .
- `clusterManager` : cung cấp khả năng quản lý và giám sát trên cluster. Một user cùng với **role** này có thể truy cập database `config` và `local` - được sử dụng trong **sharding** và **replication**
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#clusterManager)
- `clusterMonitor` : cung cấp truy cập read-only tới các công cụ giám sát, như **MongoDB Cloud Manager** và **Ops Manager monitoring agent**
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#clusterMonitor)
- `hostManager` : cung cấp khả năng quản lý và giám sát server
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#hostManager)
#### **1.1.4) Các role cho backup và restore**
- `backup` : cung cấp đặc quyền tối thiểu cần thiết để backup dữ liệu. **Role** này cung cấp cả đặc quyền sử dụng  **MongoDB Cloud Manager backup agent**, **Ops Manager backup agent** hoặc sử dụng `mongodump` để backup toàn bộ mongod instance. Cung cấp thêm thao tác `insert` và `update` trên collection `mms.backup` trên database `admin` và trên database `config`.
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#backup)
- `restore` : cung cấp các đặc quyền cần thiết để khôi phục dữ liệu từ bản backup nếu data không bao gồm collection `system.profile`
    - [Các thao tác được thực hiện](https://docs.mongodb.com/manual/reference/built-in-roles/#restore)
#### **1.1.5) Các role trên tất cả các database**
- `readAnyDatabase` : cung cấp đặc quyền read-only trên tất cả các database ngoại trừ `local` và `config`. **Role** cũng cung cấp thao tác `listDatabases` trên cluster 
- `readWriteAnyDatabase` : cung cấp đặc quyền readWrite trên tất cả các database ngoại trừ `local` và `config`. **Role** cũng cung cấp thao tác `listDatabases` trên cluster 
- `userAdminAnyDatabase` : cung cấp quyền truy cập và quản trị tương tự như `userAdmin` trên tất cả các database ngoại trừ `local` và `config` .
#### **1.1.6) Superuser Role**
- `root`
#### **1.1.7) Internal Role**
- `__system`
### **1.2) User tự tạo role**
- Để thêm 1 **role**, sử dụng phương thức `db.createRole()` .
- Để update **role**, sử dụng phương thức `db.updateRole()`
- [Danh sách các phương thức có thể gán cho **role**](https://docs.mongodb.com/manual/reference/method/#role-management-methods)
- Tất cả **role** được lưu trong collection `system.roles`
- Khi thêm 1 **role**, có nghĩa là tạo **role** trên 1 database cụ thể. **MongoDB** sử dụng sự kết hợp giữa database và teenn **role** để định danh duy nhất cho 1 **role**
### **1.3) Tạo role**
- Cú pháp :
    ```
    > db.createRole(role, writeConcern)
    ```
    - Trong đó :
        - `role` : một document chứa tên của **role** và định nghĩa role.
        - `writeConcern` : Tùy chọn. Mức độ `writeConcern` muốn áp dụng cho thao tác. 
- **VD :** 
    ```
    > db.createRole(
        {
            role: "myClusterwideAdmin",
            privileges: [
                { resource: { cluster: true }, actions: [ "addShard" ] },
                { resource: { db: "config", collection: "" }, actions: [ "find", "update", "insert", "remove" ] },
                { resource: { db: "users", collection: "usersCollection" }, actions: [ "update", "insert", "remove" ] },
                { resource: { db: "", collection: "" }, actions: [ "find" ] }
            ],
            roles: [
                { role: "read", db: "admin" }
            ]
        },
        { w: "majority" , wtimeout: 5000 }
    )
    ```
### **1.4) Xem thông tin role**
- Cú pháp :
    ```
    > db.getRole(NAME)
    ```
    hoặc sử dụng lệnh `show roles` để list toàn bộ các user.
- **VD :**
    ```
    > db.getRole("read")
    {
            "role" : "read",
            "db" : "mydb",
            "isBuiltin" : true,
            "roles" : [ ],
            "inheritedRoles" : [ ]
    }
    ```
### **1.5) Xóa role**
- Cú pháp :
    ```
    > db.dropRole(NAME)
    ```
    hoặc sử dụng lệnh `db.dropAllRoles()` để xóa toàn bộ các **role** mà user tự định nghĩa.
## **2) Quản lý User**
### **2.1) Kích hoạt xác thực trên MongoDB**
- Mặc định, sau khi cài đặt, có thế truy cập **MongoDB** mà không cần username/password. Tuy nhiên nhiều trường hợp ta cần sử dụng account(username/password) để quản lý việc truy cập database (cho phép read, write, update,..) thì ta cần bật chức năng xác thực (ahthorization) của MongoDB lên.
- Để kích hoạt xác thực, cần chỉnh sửa block `security` trong file cấu hình mongodb :
    ```
    # vi /etc/mongod.conf
    ```
    - Chỉnh sửa như sau :
        ```yaml
        ...
        security:
            authorization: enabled
        ...
        ```
    - Restart dịch vụ để áp dụng cấu hình :
        ```
        # systemctl restart mongod
        ```
    - Truy cập `mongo` không qua user, ta sẽ thấy không thể thao tác được gì và gặp lỗi `Unauthorized` :
        ```
        # mongo
        MongoDB shell version v4.4.1
        connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
        Implicit session: session { "id" : UUID("5a11d2fa-36cd-4075-86cf-7cd94f3b2dcc") }
        MongoDB server version: 4.4.1
        > use mydb
        switched to db mydb
        > db.stats()
        {
                "ok" : 0,
                "errmsg" : "not authorized on mydb to execute command { dbstats: 1.0, scale: undefined, lsid: { id: UUID(\"5a11d2fa-36cd-4075-86cf-7cd94f3b2dcc\") }, $db: \"mydb\" }",
                "code" : 13,
                "codeName" : "Unauthorized"
        }
        ```
### **2.2) Tạo user**
#### **2.2.1) Tạo system user**
- **System user** là các user có quyền quản trị các user, database khác .
- Để tạo **system user**, ta sẽ tạo user trên database `admin`, các system user này có thể truy cập, chỉnh sửa các database khác nếu được cấp quyền .
    > Cần tắt tính năng xác thực khi mới bắt đầu tạo **system user** đầu tiên
- Cú pháp tạo user : 
    ```
    > db.createUser(
        {
            user: USERNAME,
            pwd: passwordPrompt(),  // or cleartext password
            roles: [
                { role: ROLE, db: DATABASE }
                ...
            ]
        }
    )
    ```
- **VD :** Tạo một user có quyền cao nhất trong **MongoDB** :
    ```
    > use admin
    > db.createUser(
        {
            user: "cuongnq",
            pwd: "Password123",
            roles: [
                { role: "root", db: "admin" }
            ]
        }
    )
    ```
    - Output :
    ```
    Successfully added user: {
            "user" : "cuongnq",
            "roles" : [
                    {
                            "role" : "root",
                            "db" : "admin"
                    }
            ]
    }
    ```
    - Bật tính năng xác thực và đăng nhập với user vừa tạo :
    ```
    # mongo -u cuongnq -p Password123
    MongoDB shell version v4.4.1
    connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
    Implicit session: session { "id" : UUID("57220b4a-fe1d-4566-b38f-6cf0be73ce05") }
    MongoDB server version: 4.4.1
    ---
    >
    ```
### **2.2.2) Tạo user thường**
- **User thường** là các user thường dùng để gán quyền riêng cho các database.
- Cú pháp tạo user tương tự như trên 
- **VD :** Tạo user `trangptt` chỉ có quyền `read` trên collection `users`  :
    ```
    > use mydb
    > db.createUser(
        {
            user: "trangptt",
            pwd: "Password123",
            roles: [
                { role: "read", db: "mydb", collection: "users" }
            ]
        }
    )
    ```
    - Kiểm chứng :
        - Đăng nhập qua user `trangptt`. Lưu ý cần thêm option `--authenticationDatabase` khi xác thực với database cụ thể
            ```
            # mongo -u trangptt -p Password123 --authenticationDatabase mydb
            MongoDB shell version v4.4.1
            connecting to: mongodb://127.0.0.1:27017/?authSource=mydb&compressors=disabled&gssapiServiceName=mongodb
            Implicit session: session { "id" : UUID("c2d7f398-c4dd-485a-8600-e44e858c1855") }
            MongoDB server version: 4.4.1
            >
            ```
        - Show database. Ta thấy user `trangptt` chỉ nhìn thấy đúng database mà nó có quyền :
            ```
            > show dbs
            mydb  0.038GB
            > use mydb
            ```
        - Thử read và write trên collection `users` :
            ```
            > db.users.find().limit(2)                                    
            { "_id" : ObjectId("5f8c0046a66e687b66e48c49"), "i" : 0, "username" : "user0", "age" : 28 }
            { "_id" : ObjectId("5f8c0046a66e687b66e48c4a"), "i" : 1, "username" : "user1", "age" : 68 }
            ```
            > Thao tác `read` vẫn được vì đã cấp quyền
            ```
            > db.users.insert({"i": 2, "username": "user2", "age": 24})
            WriteCommandError({
                    "ok" : 0,
                    "errmsg" : "not authorized on test to execute command { insert: \"users\", ordered: true, lsid: { id: UUID(\"93b2a843-573e-4904-b61e-3b02e625b5ac\") }, $db: \"test\" }",
                    "code" : 13,
                    "codeName" : "Unauthorized"
            })
            ```
            > Thao tác `write` không thực hiện được
### **2.3) Xem thông tin User**
- Cú pháp :
    ```
    > db.getUser(USERNAME)
    ```
    hoặc sử dụng lệnh `show users` để list toàn bộ các user.
- **VD :**
    ```
    > use mydb
    > db.getUser('trangptt')
    {
            "_id" : "mydb.trangptt",
            "userId" : UUID("0ef022b2-880b-458c-a8d5-cf2e86985f4e"),
            "user" : "trangptt",
            "db" : "mydb",
            "roles" : [
                    {
                            "role" : "read",
                            "db" : "mydb"
                    }
            ],
            "mechanisms" : [
                    "SCRAM-SHA-1",
                    "SCRAM-SHA-256"
            ]
    }
    ```
### **2.4) Thay đổi role của user**
#### **2.4.1) Gán thêm role**
- Cú pháp :
    ```
    > db.grantRolesToUser(
        USERNAME,
        [
            { role: ROLE, db: DATABASE }
        ]
    )
    ```
#### **2.4.2) Bỏ role đã gán**
- Cú pháp :
    ```
    > db.revokeRolesFromUser(
        USERNAME,
        [
            { role: ROLE, db: DATABASE }
        ]
    )
    ```
### **2.5) Thay đổi password user**
- Cú pháp :
    ```
    > db.changeUserPassword(USERNAME, NEW_PASSWORD)
    ```
### **2.6) Xóa user**
- Cú pháp :
    ```
    > db.dropUser(USERNAME)
    ```
---------------------------
Tham khảo 
- [Authentication](https://docs.mongodb.com/manual/core/authentication/)
- [RBAC](https://docs.mongodb.com/manual/core/authorization/)