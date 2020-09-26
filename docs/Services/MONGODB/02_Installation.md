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