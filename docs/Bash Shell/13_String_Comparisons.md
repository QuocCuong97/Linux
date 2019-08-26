# So sánh chuỗi trong Bash
- So sánh chuỗi :
    - `string1 = string2` : true nếu 2 chuỗi bằng nhau ( chính xác từng ký tự )
    - `string1 != string2` : true nếu 2 chuỗi không giống nhau
    - `-n string1` : true nếu `string1` không rỗng
    - `-z string1` : true nếu `string1` rỗng
- **VD :**
    ```bash
    #!/bin/bash
    
    read -p "Enter first string: " str1
    read -p "Enter second string: " str2
    if [ "$str1" == "$str2" ]
    then
    echo "Both strings are same"
    else
    echo "Both strings are different"
    fi
    ```