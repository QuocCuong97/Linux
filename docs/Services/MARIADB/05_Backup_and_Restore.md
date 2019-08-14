# Backup and Restore
>## **Backup thủ công với `mysqldump`**
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
>## **Backup tự động với `Crontab`**