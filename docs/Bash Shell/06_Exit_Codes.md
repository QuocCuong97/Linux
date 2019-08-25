# Bash Exit Codes
- **`Exit code`** là 1 số nằm trong khoảng từ `0` đến `255` .
- Đây là giá trị trả về cho **parent process** sau khi **child process** hoàn thành .
- Nói cách khác , nó trả về **`exit status`** của lệnh cuối cùng vừa thực hiện . Giá trị **`exit status`** này sẽ cho biết lệnh thực hiện có thành công hay không :
    - `0` : lệnh thực hiện thành công ( *success* )
    - `giá_trị_khác_0` : lệnh thực hiện không thành công ( *failure* )
- **VD1 :**
    ```bash
    #!/bin/bash
    
    echo "hi" > /tmp/tesfile.txt
    
    if [ $? -eq 0 ]; then
    echo "Hurrey. it works"
    else
    echo "Sorry, can't write /tmp/tesfile.txt"
    fi
    ```
- **VD2 :**
    ```bash
    #!/bin/bash
    
    STRING="tecadmin"
    
    if grep ${STRING} /etc/passwd
    then
    echo "Yeah! string found"
    else
    echo "Ooooh, no matching string found"
    fi
    ```