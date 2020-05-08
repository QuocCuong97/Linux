# Backup and Restore
>## **Backup với `mysqldump`**
- Cấu trúc lệnh :
    ```
    # mysqldump -u [username] -p [databaseName] > [filename]-$(date +%F).sql
    ```
- `mysqldump` sẽ hỏi mật khẩu của user có quyền sao lưu .
- Dựa vào kích cỡ của **Database** , có thể sẽ mất 1 lúc để hoàn thành .
- **Database** được backup sẽ được tạo ở thư mục hiện hành khi thực hiện lệnh .
- `-$(date +%F)` thêm mốc thời gian bakup cho file .
- **VD1 :** Backup toàn bộ **DBMS** ( tất cả các **Database** hiện có ) :
    ```
    # mysqldump --all-databases --single-transaction --quick --lock-tables=false > full-backup-$(date +%F).sql -u root -p
    ```
    - Trong đó :
        - `--single-transaction` : thực hiện lệnh `BEGIN SQL` trước khi tiến hành backup
        - `--quick` : thực hiện cơ chế backup an toàn từng dòng một trong từng **Table**
        - `--lock-table=false` : không lock **Table** gốc trong suốt quá trình backup
- **VD2 :** Backup một **Database** cụ thể : (**VD :** `mariadbtest`) :
    ```
    # mysqldump -u username -p mariadbtest --single-transaction --quick --lock-tables=false > mariadbtest-backup-$(date +%F).sql
    ```
- **VD3 :** Backup một **Table** của một **Database** : (**VD :** `mariadbtest` - **Table** : `name`)
    ```
    # mysqldump -u username -p --single-transaction --quick --lock-tables=false mariadbtest name > mariadbtest-name-$(date +%F).sql
    ```
> ## **Backup thủ công**
- **B1 :** Dừng dịch vụ `mysql` :
    ```
    # systemctl stop mysql
    ```
- **B2 :** Tạo thư mục chứa file backup :
    ```
    # mkdir /opt/db-backups
    ```
- **B3 :** Gom tất cả các file **Database** cần backup vào 1 file `tar` :
    ```
    # tar -cfvz /opt/db-backups/db-$(date +%F).tar.gz /var/lib/mysql/*
    ```
- **B4 :** Khởi động lại dịch vụ :
    ```
    # systemctl restart mysql
    ```
>## **Backup tự động với `Crontab`**
- **B1 :** Tạo file lưu trữ username và password của user quản trị **MariaDB** ( đặt trên thư mục home của user ):
    ```
    # vi /root/.mylogin.cnf
    ```
    - Thêm vào nội dung sau :
        ```
        [client]
        user = root
        password = P@ssw0rd
        ```
- **B2 :** Phân quyền `600` để bảo vệ cho file :
    ```
    # chmod 600 /root/.mylogin.cnf
    ```
- **B3 :** Tạo **crontab** :
    ```
    # crontab -e
    ```
    - Thêm vào nội dung sau :
        - **TH1 :** Backup toàn bộ **Database** vào `1h` mỗi ngày :
            ```
            0 1 * * * /usr/bin/mysqldump --defaults-extra-file=/root/.mylogin.cnf -u root --single-transaction --quick --lock-tables=false --all-databases > full-backup-$(date +\%F).sql
            ```
        - **TH2 :** Backup 1 **Database** cụ thể vào `17h30` mỗi ngày :
            ```
            30 17 * * * /usr/bin/mysqldump --defaults-extra-file=/root/.mylogin.cnf -u root --single-transaction --quick --lock-tables=false mariadbtest > mariadbtest-backup-$(date +\%F).sql
            ```
        - **TH3 :** Backup một **Database** cụ thể : (**VD :** `mariadbtest`) :
            ```
            30 17 * * * /usr/bin/mysqldump --defaults-extra-file=/root/.mylogin.cnf -u root --single-transaction --quick --lock-tables=false mariadbtest name > mariadbtest-name-backup-$(date +\%F).sql
            ```
> ## **Khôi phục từ các bản Backup `.sql`**
- Cấu trúc lệnh :
    ```
    # mysql -u [username] -p [databaseName] < [filename].sql
    ```
- **TH1 :** Khôi phục toàn bộ các **Database** sau khi được full-backup :
    ```
    # mysql -u root -p < full-backup.sql
    ```
- **TH2 :** Khôi phục 1 **Database** cụ thể :
    ```
    # mysql -u root -p mariadbtest < mariadbtest-backup.sql
    ```
- **TH3 :** Khôi phục 1 **Table** cụ thể trong **Database** :
    ```
    # mysql -u root -p name < mariadbtest-name-backup.sql
    ```
>## **Khôi phục từ các bản Backup `.tar.gz`**
- **B1 :** Dừng dịch vụ `mysql` :
    ```
    # systemctl stop mysql
    ```
- **B2 :** Giải nén thư mục chứa file backup :
    ```
    # tar zxvf /opt/db-backups/db-archive.tar.gz -C /tmp/db/
    ```
- **B3 :** Backup thư mục lưu trữ **Database** cũ :
    ```
    # mv /var/lib/mysql /var/lib/mysql-old
    # mkdir /var/lib/mysql
    ```
- **B4 :** Copy file từ thư mục vừa giải nén vào thư mục chứa **Database** :
    ```
    # mv /tmp/db/* /var/lib/mysql
    ```
- **B5 :** Cấp quyền lại cho thư mục **Database** :
    ```
    # chown -R mysql:mysql /var/lib/mysql
    ```
- **B6 :** Restart lại dịch vụ :
    ```
    # systemctl restart mysql
    ```
