# Mảng ( array )
- Mảng là 1 cấu trúc dữ liệu bao gồm nhiều phần tử có tính chất chung .
- Mỗi thành phần của mảng có thể được truy cập qua 1 số id có thứ tự ( ***key index number*** )
### **Định nghĩa 1 mảng trong Bash**
- Có 2 cách để tạo mảng trong Bash Script
    - **C1 :** Sử dụng lệnh `declare` :
        ```bash
        declare -a test_array
        ```
    - **C2 :** Liệt kê các phần tử của mảng :
        ```bash
        test_array=(apple orange lemon)
        ```
### **Truy cập các phần tử của mảng**
- Giống như các ngôn ngữ lập trình khác , phần tử của mảng trong Bash có thể được truy cập thông qua `số id` bắt đầu từ `0` đến `1`,`2`,`3`,`.....`,`n` .
    ```bash
    echo ${test_array[0]}

    apple
    ```
- Để show tất cả các phần tử của mảng , sử dụng `số id` là `@` hoặc `*` :
    ```bash
    echo ${test_array[@]}

    apple orange lemon
    ```
### **Tạo vòng lặp cho mảng**
- Có thể truy cập các phần tử của mảng bằng cách sử dụng vòng lặp trong Bash Script .
    ```bash
    for i in ${test_array[@]}
    do
    echo $i
    done
    ```
    => Output :
    ```
    apple orange lemon
    ```
### **Thêm phần tử vào mảng**
- Có thể thêm các phần tử khác vào mảng bằng cách sử dụng toán tử `+=` :
    ```bash
    test_array+=(mango banana)
    ```
    Show lại các phần tử :
    ```bash
    echo ${test_array[@]}

    apple orange lemon mango banana
    ```
### **Update giá trị phần tử của mảng**
- Để update giá trị phần tử của mảng , chỉ đơn giản gọi phần tử ra và gán giá trị mới cho nó :
    ```bash
    test_array[2]=grapes
    ```
    Show lại các phần tử :
    ```bash
    echo ${test_array[@]}

    apple orange grapes mango banana
    ```
### **Xóa phần tử của mảng**
- Có thể xóa phần tử của mảng bằng cách sử dụng `số id` của phần tử muốn xóa cùng với lệnh `unset` :
    ```bash
    unset test_array[2]
    ```
    Show lại các phần tử :
    ```bash
    echo ${test_array[@]}

    apple orange mango banana
    ```