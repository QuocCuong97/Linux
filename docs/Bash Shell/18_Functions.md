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
- **VD1 :** Tạo hàm ( không có tham số kèm theo ) :
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
- **VD2 :** Hàm đi kèm tham số :
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
> [Còn nữa](https://tecadmin.net/how-to-use-functions-in-shell-scripts/)
