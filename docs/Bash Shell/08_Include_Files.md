# Lồng ghép file trong Bash
- Giống như các ngôn ngữ lập trình khác có thể lồng file trong file , **bash script** cũng cung cấp tính năng lồng 1 **script** khác trong **script** cần chạy .
- Cấu trúc :
    ```bash
    #!/bin/bash
    
    source other-file.sh
    ```
### **Lồng shell script vào 1 script khác** 
- **VD :** Đầu tiên có file `data.sh` :
    ```bash
    #!/bin/bash
    
    id=100
    name="Nhanhoa"
    ```
- Tạo 1 file `show.sh` , bao gồm file `data.sh` trong đó :
    ```bash
    #!/bin/bash
    
    source data.sh
    
    echo "Welcome to $name"
    echo "Your id is $id"
    ```
- Thực hiện chạy script :
    ```
    # chmod +x data.sh show.sh
    # ./show.sh
    ```
    => Output :
    ```
    Welcome to Nhanhoa
    Your id is 100
    ```