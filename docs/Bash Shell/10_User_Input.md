# Dữ liệu nhập vào trong Bash
- Sử dụng lệnh `read` để tương tác với input mà user nhập vào khi chạy script .
- Cấu trúc :
    ```bash
    read variable_name
    ```
- **VD1 :** Có script sau :
    ```bash
    #!/bin/bash

    echo "Enter your name:"
    read myname
    echo "Hello" $myname
    ```
    => Output :
    ```
    Enter your name : Cuong     ( đợi input `Cuong` từ bàn phím )
    Hello Cuong
    ```
- **VD2 :** Các **options** của `read` :
    - `-p` : hiển thị input lên màn hình
        ```bash
        read -p "Enter your username: " myname
        ```
    - `-sp` : không hiển thị input lên màn hình , thường dùng cho password
        ```bash
        read -sp "Enter your password: " mypassword
        ```
    - Xét VD :
        ```bash
        #!/bin/bash
        
        read -p "Enter your username: " myname
        read -sp "Enter your password: " mypassword
        
        echo -e "\nYour username is $myname and Password is $mypassword"
        ```
        => Output :
        
        <img src=https://i.imgur.com/7cZNqQ3.png>