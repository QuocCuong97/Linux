# Yes/No Question
## **One-time Script**
- Script này chỉ chạy 1 lần và đưa ra kết quả dù cho user đưa vào input gì .
    ```bash
    #!/bin/bash
    
    read -r -p "Are You Sure? [Y/n] " input
    
    case $input in
        [yY][eE][sS]|[yY])
    echo "Yes"
    ;;
        [nN][oO]|[nN])
    echo "No"
        ;;
        *)
    echo "Invalid input..."
    exit 1
    ;;
    esac
    ```
## **Loop Script**
- Script này sẽ lặp đi lặp lại cho đến khi người dùng nhập đúng nội dung được đưa ra trong điều kiện `Case`
    ```bash
    #!/bin/bash
    
    while true
    do
    read -r -p "Are You Sure? [Y/n] " input
    
    case $input in
        [yY][eE][sS]|[yY])
    echo "Yes"
    ;;
        [nN][oO]|[nN])
    echo "No"
            ;;
        *)
    echo "Invalid input..."
    ;;
    esac
    done
    ```