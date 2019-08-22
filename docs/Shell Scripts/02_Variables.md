# Biến trong shell
## **1) Biến hệ thống**
- Tạo ra bởi hệ thống và quản lý bởi Linux .
- Tên biến là chữ hoa
- Để show các biến hệ thống , dùng lệnh `env` hoặc `printenv`
- Một số biến hệ thống quan trọng :
    - `BASH` = `/bin/bash` - tên **shell**
    - `BASH_VERSION` = `1.14.7(1)` - phiên bản của **shell**
    - `COLUMN` = `80` - số cột cho màn hình
    - `HOME` = `/home/cuongnq` - thư mục `home` của user
    - `LINE` = `25` - số dòng của màn hình
    - `LOGNAME` = `cuongnq` - tên đăng nhập của user
    - `OSTYPE` = `Linux` - loại hệ điều hành
    - `PATH` = `/usr/bin:/sbin:/bin:/usr/sbin` - thiết lập đường dẫn của biến môi trường
    - `PWD` = `/root` - thư mục hiện hành
    - `SHELL` = `/bin/bash` - tên shell
    - `USER` = `root` - user đang login
- Cách gọi biến hệ thống :
    ```
    # echo $HOME
    # echo $USER
    ```
## **2) Biến do người dùng tạo ra**
- Cú pháp : `tên_biến` = `value`
- Tên biến phải bắt đầu bằng ký tự và ngăn cách giữa các ký tự bằng dấu "`_`"
- Không có dấu cách 2 bên toán tử "`=`" khi gán giá trị cho biến .
    ```
    a=1    -> Đúng
    a= 1 hoặc a =1     -> Sai
    ```
- Tên biến có phân biệt chữ HOA và chữ thường .
- Một biến không có giá trị khởi tạo thì giá trị bằng NULL .
- Không dùng dấu "`?`" , "`*`" để đặt tên các biến .
- Lệnh `echo` để in ra các giá trị của biến :
    ```
    # echo [options] [string,variable,...]
    ```
    - **Options :**
        - `-n` : không in kí tự xuống dòng 
        - `-e` : cho phép hiểu những kí tự theo sau dấu "`\`"
            - `\a` : alert ( tiếng chuông )
            - `\b` : backspace ( xóa kí tự trước )
            - `\c` : không xuống dòng
            - `\e` : xóa ký tự tiếp theo
            - `\f` : form feed
            - `\n` : xuống dòng
            - `\r` : về đầu dòng
            - `\t` : tab ngang
            - `\v` : xuống cách 1 dòng
            - `\\` : dấu `\`
    - **VD :**
        ```
        # echo -e "Hello\tTuan"
        Hello   Tuan
        # echo -e "Hello\nTuan"
        Hello
        Tuan
        ```
## **3) Các biến đặc biệt**
- `$0` : tên file của sript
- `$#` : số biến có trong script
- `$$` : số PID khi script được thực hiện
- `$?` : exit status code của lệnh trước
- `$!` : 