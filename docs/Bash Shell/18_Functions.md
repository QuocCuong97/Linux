# Hàm ( function ) trong Bash
- Một ***hàm*** - hay cũng có thể gọi là 1 ***chương trình con*** là một khối lệnh được sử dụng cho 1 tác vụ cụ thể .
- Hàm có 1 đặc tính là khả năng tái sử dụng lại ( ***re-usability*** )
- Cấu trúc :
    ```bash
    funcationName(){    
    // scope of function
    }

    functionName  //calling of function
    ```
- **VD :** Tạo hàm ( không có tham số kèm theo ) :
    ```bash
    #!/bin/bash

    funHello(){
        echo "Hello World!";
    }

    # Call funHello from anywhere in the script like below

    funHello
    ```
    Chạy Script :
    ```
    # chmod +x test_1.sh
    # ./test_1.sh
    ```
    => Output :
    ```
    Hello World!
    ```
### **Tạo Hàm đi kèm tham số**
- **VD :** Có script sau :
    ```bash
    #!/bin/bash

    funArguments(){
    echo "First Argument: " $1
    echo "Second Argument: " $2
    echo "Third Argument: " $3
    echo "Fourth Argument: " $4
    }

    # Call funArguments from anywhere in the script using parameters like below

    funArguments 1 Welcome to Nhanhoa
    ```
    Chạy Script :
    ```
    # chmod +x test_2.sh
    # ./test_2.sh
    ```
    => Output :
    ```
    First Argument: 1
    Second Argument: Welcome
    Third Argument: to
    Fourth Argument: Nhanhoa
    ```
### **Trả về kết quả của hàm trong Shell**
- **VD :** Có script sau :
    ```bash
    #!/bin/bash

    funReturnValues(){
    echo 5
    }

    # Call funReturnValues from any where in script and get return values

    values=$(funReturnValues)
    echo "Return value is : $values"
    ```
    => Output :
    ```
    5
    ```
### **Tạo hàm đệ quy trong Shell**
- Hàm mà có thể tự gọi được nó gọi là **hàm đệ quy** ( ***recursive function*** ) .
- **VD :** Có script sau :
    ```bash
    #!/bin/bash

    funRecursive(){
    val=$1
    if [ $val -gt 5 ]
    then
        exit 0
    else
        echo $val
    fi
    val=$((val+1))
    funRecursive $val     # Function calling itself here
    }

    # Call funRecursive from any where in script

    funRecursive 1
    ```
    => Output :
    ```
    1
    2
    3
    4
    5
    ```

