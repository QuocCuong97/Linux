# Debug một Bash Script
- **Bash** cung cấp 1 tùy chọn để **`debug`** script ngay lúc nó chạy :
    ```
    # bash -x script.sh
    ```
- Có thể sử dụng lệnh `set -x` ngay trong script để kích hoạt chế độ **`debug`** mỗi khi chạy :
    ```bash
    #!/bin/bash

    set -x   # this line will enable debug

    cd /var/log/
    for i in "*.log"; do
    du -sh $i
    done
    ```
    Chạy script :
    ```
    ./ script.sh
    ```
    => Output : 
    ```
    cd /var/log/
    + cd /var/log/
    for i in "*.log"; do
    du -sh $i
    done
    + for i in '"*.log"'
    + du -sh boot.log mysqld.log post111.log post1121.log yum.log
    0       boot.log
    32K     mysqld.log
    0       post111.log
    0       post1121.log
    4.0K    yum.log
    ```