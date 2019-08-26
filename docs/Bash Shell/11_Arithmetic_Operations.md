# Các phép toán số học trong Bash
### **Lệnh `expr`**
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
### **Các dấu ngoặc**
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
### **Các phép toán kiểm tra**
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

