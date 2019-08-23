# Các phép toán
## **1) Các phép toán số học**
### **1.1) Lệnh `expr`**
- Cú pháp :
    ```
    # expr toán_hạng_1 toán_tử toán_hạng_2
    ```
    - Toán tử :
        - `+` : cộng
        - `-` : trừ
        - `\*` : nhân
        - `/` : chia lấy phần nguyên
        - `%` : chia lấy phần dư
- **VD :** 
    ```
    # expr 10 \* 2
    20
    ```
> ***Chú ý :** Phải có dấu cách trước và sau toán tử*
### **1.2) Các dấu ngoặc**
- Tất cả các ký tự trong dấu ngoặc kép đều không có ý nghĩa tính toán , trừ những ký tự sau `\` hoặc `$` .
- Dấu nháy ngược `(``)`  : yêu cầu thực thi lệnh
- **VD :** 
    ```
    # echo "ngay hom nay la : `date`"
    ngay hom nay la : Friday May 24 08:27:49 +07 2019
    # echo `expr 1 + 2`
    3
    # echo "expr 1 + 2"
    expr 1 + 2
    ```
### **1.3) Kiểm tra trạng thái trả về của lệnh vừa thực hiện**
- Cú pháp :
    ```
    # echo $?
    ```
    - **Output :** 
        - `1` : Lệnh trước đó có lỗi
        - `0` : Lệnh trước đó không có lỗi
- **VD :** File `abc.txt` không tồn tại
    ```
    # rm abc.txt
    # echo $?
    1            -> Lệnh trước có lỗi ( xóa file không tồn tại )
    ```
## **2) Các phép toán kiểm tra**
- So sánh biến số :
    - `-eq` ( `==` ) : bằng ( *equal* )
    - `-ne` ( `!=` ) : khác ( *not equal* )
    - `-lt` : nhỏ hơn ( *less than* )
    - `-le` : nhỏ hơn hoặc bằng ( *less or equal* )
    - `-ge` : lớn hơn hoặc bằng ( *greater of equal* )
    - `-gt` : lớn hơn ( *greater than* )
- So sánh chuỗi :
    - `string1 = string2` : true nếu 2 chuỗi bằng nhau ( chính xác từng ký tự )
    - `string1 != string2` : true nếu 2 chuỗi không giống nhau
    - `-n string1` : true nếu `string1` không rỗng
    - `-z string1` : true nếu `string1` rỗng
- Kiểm tra điều kiện trên tập tin :
    - `d file` : true nếu file là thư mục
    - `e file` : true nếu file có tồn tại
    - `f file` : true nếu file là tập tin thường
    - `s file` : true nếu kích cỡ file khác `0`
    - `u file` : true nếu **SUID** được thiết lập trên file
    - `g file` : true nếu **SGID** được thiết lập trên file
    - `r file` : true nếu file cho phép **read**
    - `w file` : true nếu file cho phép **write**
    - `x file` : true nếu file cho phép **execute**
- Toán tử logic :
    - `! expression` : NOT
    - `expression1 , -a , expression2` : AND
    - `expression1 && expression2` : AND
    - `expression1 , -0 , expression2` : OR
    - `expression1 || expression2` : OR