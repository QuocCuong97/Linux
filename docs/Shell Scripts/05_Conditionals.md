# Cấu trúc rẽ nhánh **`if`**
### **`Simple if`**
- Cú pháp 
    ```bash
    if điều_kiện
    then
        command1
        ....
    fi
    ```
### **`If...else`**
- Cú pháp :
    ```bash
    if điều_kiện then
        command1
        ....
    else
        command2
        ....
    fi
    ```
### **`If...elif...else`**
- Cú pháp :
    ```bash
    if điều_kiện then
        command1
        ....
    elif điều_kiện then
        command2
        ....
    else
        command3
        ....
    fi
    ```
### **`Nested if`**
- Cú pháp :
    ```bash
    if điều_kiện then
        command1
        ....
    else
        if điều_kiện then
            command2
            ....
        else
            command3
            ....
        fi
    fi
# Cấu trúc điều kiện **`case`**
- Cú pháp :
    ```bash
    case $bien in
        case1.1/case1.2) statement1...
        case2) statement2...
        ...
    esac
    ```