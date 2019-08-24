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