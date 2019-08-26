# Cấu trúc Switch ( `case` ) trong Bash
- Mệnh đề `case` rất hữu ích và sẽ xử lý nhanh hơn so với cấu trúc `else-if ladder` . Thay vì phải check tất cả các điều kiện `if-else` thì cấu trúc `case` sẽ đi đến thẳng khối lệnh cần thực hiện theo condition trong input .
- Cú pháp :
    ```bash
    case $bien in
        case1.1/case1.2) statement1...
        case2) statement2...
        ...
    esac
    ```
- **VD :** Có Script sau :
    ```bash
    #!/bin/bash
    
    read -p "Enter your choice [yes/no]:" choice
    
    case $choice in
        yes)
            echo "Thank you"
            echo "Your type: Yes"
            ;;
        no)
            echo "Ooops"
            echo "You type: No"
            ;;
        *)
            echo "Sorry, invalid input"
            ;;
    esac
    ```
    Chạy Script :
    ```
    chmod +x case1.sh
    ./case1.sh
    Enter your choice [yes/no]:yes
    Thank you
    Your type: Yes


    ./case1.sh
    Enter your choice [yes/no]:no
    Ooops
    You type: No

    ./case1.sh
    Enter your choice [yes/no]:anything
    Sorry, invalid input
    ```
### **Lồng nhiều chuỗi vào trong 1 `case` options**
- **VD :**
    ```bash
    #!/bin/bash
    
    read -p "Enter your choice [yes/no]:" choice
    
    case $choice in
        Y/y/Yes/YES/yes)
            echo "Thank you"
            echo "Your type: Yes"
            ;;
        N/n/No/NO/no)
            echo "Ooops"
            echo "You type: No"
            ;;
        *)
            echo "Sorry, invalid input"
            ;;
    esac
    ```
### **Các trường hợp đặc biệt với `case`**
- Có thể sử dụng các ký tự đặc biệt như `*` , `?` và `[]` để match những trường hợp đặc biệt trong `case` .
- Sử dụng tùy chọn `shopt -s` để sử dụng tùy chọn này .
- **VD :**
    ```bash
    #!/bin/bash
    
    read -p "Enter a string:" choice
    shopt -s extglob
    case $choice in
        a*)                    ### matches anything starting with "a"
            #script here
            ;;
    
        b?)                    ### matches any two-character string starting with "b"
            #script here
            ;;
    
        s[td])                 ### matches "st" or "sd"
            #script here
            ;;
    
        r[ao]m)                ### matches "ram" or "rom"
            #script here
            ;;
    
        me?(e)t)               ### matches "met" or "meet"
            #script here
            ;;
    
        @(a|e|i|o|u))          ### matches one vowel
            #script here
            ;;
    
        *)                     ### Catchall matches anything not matched above
            #script here
            ;; 
    esac
    ```
