# Cấu trúc vòng lặp **`for`**
- Cú pháp :
    ```bash
    for tên_biến in danh_sách
    do
        command1
        ....
    done
    ```
    hoặc
    ```bash
    for (( expr1 ; expr2 ; expr3 ))
    do
        command1
        ....
    done
    ```
- **VD1 :**
    ```bash
    for i in 1 2 3 4 5
    do
        echo $i
    done
    ```
    ```
    => Output : 1 2 3 4 5
    ```
- **VD2 :**
    ```bash
    for (( i=0 ; i<=5 ; i++ ))
    do
        echo $i
    done
    ```
    ```
    => Output : 1 2 3 4 5
    ```
# Cấu trúc vòng lặp **`while`**
- Cú pháp :
    ```bash
    while [điều_kiện]
    do 
        command1
        ....
    done
    ```
- **VD :**
    ```bash
    #!!/bin/sh
    echo " Nhap vao cac so can tinh tong , nhap so am de exit "
    sum = 0
    read i
    while [ $i -ge 0 ]            # nếu i>=0
    do
        sum = `expr $sum + $i`
        read i                    # nhận giá trị từ người dùng
    done
    echo " Tong : $sum ."
    ```
    ```bash
    => Output : Nhap vao cac so de tinh tong , nhap vao cac so am de exit
    1
    5
    4
    -1
    Tong : 10 . 
    ```
> ***Chú ý :***
- Để thóat khỏi các vòng lặp `for` và `while` ta dùng lệnh `break` . Lệnh `break (n)` cho phép ra khỏi `n` mức của vòng lặp .
- Lệnh `continue` cho phép bỏ qua các lệnh còn lại để bắt đầu chu trình mới .
# Cấu trúc vòng lặp **`until`**
- Cấu trúc :
    ```bash
    until [CONDITION]
    do
        [COMMANDS]
    done
    ```