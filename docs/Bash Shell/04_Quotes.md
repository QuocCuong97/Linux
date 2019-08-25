# Quotes trong Bash
- Đây là tiêu chuẩn để trích dẫn chuỗi ( ***string*** ) trong bất kì một ngôn ngữ lập trình nào .
- **`Quotes`** được sử dụng để làm việc với texts , tên file có chứa dấu cách .
- Có 2 dạng **`quotes`** :
    - **`Single quote`** : `'...'`
    - **`Double quote`** : `"..."`
### **Quote with String**
- Khi làm việc với `text` và `string` , không có sự khác biệt giữa **`single quote`** và **`double quote`** :
    ```bash
    #!/bin/bash
    
    echo 'String in single quote'
    echo "String in double quote"
    
    mkdir 'Dir 1'
    mkdir "Dir 2"
    ```
    => Output :
    ```
    String in single quote
    String in double quote
    ```
### **Quote with Variables**
- Khi làm việc với biến ( ***variables*** ) , sẽ có sự khác nhau giữa **`single quote`** và **`double quote`** .
- Cụ thể , cú pháp đúng của biến : `echo "$VARIABLES"`
- Nếu sử dụng **`single quote`** , sẽ không thể in ra biến
    ```bash
    #!/bin/bash

    NAME="Welcome"

    echo "$NAME"
    echo '$NAME'
    ```
    => Output :
    ```
    Welcome
    $NAME
    ```