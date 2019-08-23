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