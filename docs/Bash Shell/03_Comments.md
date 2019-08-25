# Comment trong Bash
- **`Comment`** là thành phần quan trọng trong bất cứ ngôn ngữ lập trình nào . Nó được sử dụng để mô tả chức năng của đoạn code hoặc chức năng của hàm ( ***function*** )
- Giống như các ngôn ngữ phổ biến , **bash scripts** hỗ trợ 2 loại **`comment`** :
    - **`Single line comment`**
    - **`Multi-line comment`**
### **Single line comment**
- **`Single line comment`** bắt đầu với dấu `hash` ( `#` )
    ```bash
    #!/bin/bash
    
    # This is a single-line comment
    
    echo "Hello World"
    
    # This is another single line comment
    ```
### **Multi-line comment**
- Có thể sử dụng dạng **`Multi-line comment`** trong **bash scripts** .
- Có 2 cách để thực hiện :
    - Cách thức nhất : `<<ANYSTRING` ...... `ANYSTRING`
    - Cách thứ hai : `: '` ...... `'`
    ```bash
    #!/bin/bash
    
    <<COMMENTS
    This is comment line 1
    This is comment line 2
    This is comment line 3
    COMMENTS
    
    echo "Hello World"
    
    : '
    This is comment line 1
    This is comment line 2
    This is comment line 3
    '
    ```
- **`Multi-line comment`** thường được sử dụng trong lệnh `echo` vì có thể xuống dòng giữa các dòng riêng biệt . Hoặc cũng có thể được sử dụng để nhập nội dung trong các trình quản lý services
    - **VD1 :**
        ```bash
        echo <<EOF >> test.sh
        This is comment line 1
        This is comment line 2
        This is comment line 3
        EOF
        ```
    - **VD2 :**
        ```bash
        mysql -u root <<EOF
        CREATE DATABASE $mariadb;
        CREATE USER $mariauser@$mariahost IDENTIFIED BY '$mariapass';
        GRANT ALL PRIVILEGES ON $mariadb.* TO $mariauser IDENTIFIED BY '$mariapass';
        FLUSH PRIVILEGES;
        exit;
        EOF
        ```
