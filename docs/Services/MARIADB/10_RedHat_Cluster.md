# Quy trình cài đặt MariaDB trên RedHat 7 (Galera Cluster)
## **Mô hình Galera Cluster**

<p align=center><img src=https://i.imgur.com/EJiO3E1.png></p>

## **Các bước cài đặt**
#### **Cài đặt MariaDB trên cả 3 node**
#### **Cài đặt Galera Cluster trên cả 3 node**
- Cài đặt `galera-4` :
    ```
    # yum install -y galera-4
    ```
#### **Cấu hình phân giải hostname**
- Cấu hình hostname trên các node :
    ```
    # hostnamectl set-hostname [node_1|node_2|node_3]
    # bash
    ```
- Cấu hình phân giải hostname trên các host :
    ```
    # echo "192.168.1.214 node_1" >> /etc/hosts
    # echo "192.168.1.215 node_2" >> /etc/hosts
    # echo "192.168.1.216 node_3" >> /etc/hosts
    ```
#### **Cấu hình Galera Cluster**
- Thực hiện trên cả 3 node :
    - Backup file cấu hình của **MariaDB** :
        ```
        # cp /etc/my.cnf.d/server.cnf /etc/my.cnf.d/server.cnf.bak
        ```
    - Dừng dịch vụ `mariadb` :
        ```
        # systemctl stop mariadb
        ```
- Trên `node_1`, chỉnh sửa nội dung file cấu hình `/etc/my.cnf.d/server.cnf` thành như sau :
    ```
    [server]
    [mysqld]
    bind-address=192.168.1.214

    [galera]
    wsrep_on=ON
    wsrep_provider=/usr/lib64/galera-4/libgalera_smm.so
    #add your node ips here
    wsrep_cluster_address="gcomm://192.168.1.214,192.168.1.215,192.168.1.216"
    binlog_format=row
    default_storage_engine=InnoDB
    innodb_autoinc_lock_mode=2
    #Cluster name
    wsrep_cluster_name="portal_cluster"
    # Allow server to accept connections on all interfaces.
    bind-address=192.168.1.214
    # this server ip, change for each server
    wsrep_node_address="192.168.1.214"
    # this server name, change for each server
    wsrep_node_name="node_1"
    wsrep_sst_method=rsync
    [embedded]
    [mariadb]
    [mariadb-10.5]
    ```
    - Trong đó :
        - `wsrep_cluster_address`: Danh sách các node thuộc Cluster, sử dụng địa chỉ IP
        - `wsrep_cluster_name`: Tên của cluster
        - `wsrep_node_address`: Địa chỉ IP của node đang thực hiện
        - `wsrep_node_name`: Tên node (Giống với hostname)
- Trên `node_2`, chỉnh sửa nội dung file cấu hình `/etc/my.cnf.d/server.cnf` thành như sau :
    ```
    [server]
    [mysqld]
    bind-address=192.168.1.215

    [galera]
    wsrep_on=ON
    wsrep_provider=/usr/lib64/galera/libgalera_smm.so
    #add your node ips here
    wsrep_cluster_address="gcomm://192.168.1.214,192.168.1.215,192.168.1.216"
    binlog_format=row
    default_storage_engine=InnoDB
    innodb_autoinc_lock_mode=2
    #Cluster name
    wsrep_cluster_name="portal_cluster"
    # Allow server to accept connections on all interfaces.
    bind-address=192.168.1.215
    # this server ip, change for each server
    wsrep_node_address="192.168.1.215"
    # this server name, change for each server
    wsrep_node_name="node_2"
    wsrep_sst_method=rsync
    [embedded]
    [mariadb]
    [mariadb-10.5]
    ```
- Trên `node_3`, chỉnh sửa nội dung file cấu hình `/etc/my.cnf.d/server.cnf` thành như sau :
    ```
    [server]
    [mysqld]
    bind-address=192.168.1.216

    [galera]
    wsrep_on=ON
    wsrep_provider=/usr/lib64/galera/libgalera_smm.so
    #add your node ips here
    wsrep_cluster_address="gcomm://192.168.1.214,192.168.1.215,192.168.1.216"
    binlog_format=row
    default_storage_engine=InnoDB
    innodb_autoinc_lock_mode=2
    #Cluster name
    wsrep_cluster_name="portal_cluster"
    # Allow server to accept connections on all interfaces.
    bind-address=192.168.1.216
    # this server ip, change for each server
    wsrep_node_address="192.168.1.216"
    # this server name, change for each server
    wsrep_node_name="node_3"
    wsrep_sst_method=rsync
    [embedded]
    [mariadb]
    [mariadb-10.5]
    ```
- Khởi tạo cluster và khởi động lại dịch vụ `mariadb` trên `node_1` :
    ```
    # galera_new_cluster
    # systemctl start mariadb
    # systemctl enable mariadb
    ```
- Khởi động lại dịch vụ `mariadb` trên `node_2` và `node_3` :
    ```
    # systemctl start mariadb
    # systemctl enable mariadb
    ```
- Kiểm tra lại trạng thái cluster ở `node_1` :
    ```
    [root@node_1 ~]# mysql -u root -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
    +--------------------+-------+
    | Variable_name      | Value |
    +--------------------+-------+
    | wsrep_cluster_size | 3     |
    +--------------------+-------+
    ```

<img src=https://i.imgur.com/VFPKsJo.png>