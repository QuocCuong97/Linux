# Quy trình cài đặt MongoDB trên RedHat 7
## **1) Cài đặt**
### **1.1) Cài đặt Standalone**
- **B1 :** Thêm repo của **MongoDB** :
    ```
    # vi /etc/yum.repos.d/mongodb-org-4.4.repo
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
    # vi /etc/yum.repos.d/mongodb-org-4.4.repo
    ```
- **B3 :** Cài đặt MongoDB :
    ```
    # sudo yum install -y mongodb-enterprise
    ```
- 