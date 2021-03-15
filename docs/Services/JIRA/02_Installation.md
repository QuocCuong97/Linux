# Cài đặt Jira
## **Setup Database**
- **B1 :** Cài đặt PostgreSQL 12 :
    ```
    # sudo yum -y install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
    # sudo yum -y install epel-release yum-utils
    # sudo yum-config-manager --enable pgdg11
    # sudo yum install postgresql11-server postgresql11
    # sudo /usr/pgsql-11/bin/postgresql-11-setup initdb
    # sudo systemctl enable --now postgresql-11
    ```
- **B2 :** Kiểm tra trạng thái cài đặt :
    ```
    # systemctl status postgresql-11
    ```
- **B3 :** Chỉnh sửa file `pg_hba.conf` :
    ```
    # vi /var/lib/pgsql/11/data/pg_hba.conf
    ```
    - Chỉnh sửa phần sau :
        ```
        # IPv4 local connections:
        host    all             all             127.0.0.1/32            md5
        ```
- **B4 :** Cho phép jira qua firewalld :
    ```
    sudo firewall-cmd --add-service=postgresql --permanent
    sudo firewall-cmd --reload
    ```

- **B4 :** Restart dịch vụ :
    ```
    # systemctl restart postgresql-11
    ```
- **B5 :**  :
    ```
    # su - postgres
    -bash-4.2$ createuser --interactive --pwprompt     # khởi tạo user Jira
    ```
- **B6 :** Tạo database for jira :
    ```
    -bash-4.2$ psql
    > create database jira;
    > GRANT ALL PRIVILEGES ON database jira TO jirauser;
    ```
> Chú ý :
- Start dịch vụ
    ```
    /opt/atlassian/jira/bin/start-jira.sh
    ```
- Stop dịch vụ
    ```
    /opt/atlassian/jira/bin/stop-jira.sh
    ```