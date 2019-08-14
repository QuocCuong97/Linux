# Các kiểu dữ liệu trong Database
## **1) NUMERIC ( *số học* )**
### **1.1) Boolean**
- Cấu trúc :
    ```
    BOOL, BOOLEAN
    ```
- `0` được coi là giá trị `FALSE` và các giá trị khác là `TRUE`
### **1.2) TINYINT**
- Cấu trúc : `TINYINT(M)`
- Là 1 số nguyên ( ***integer*** ) rất nhỏ .
- Vùng `SIGNED` : từ `-128` đến `127`
- Vùng `UNSIGNED` : từ `0` đến `255`
- Vùng `ZEROFILL` :
### **1.3) SMALLINT**
- Cấu trúc : `SMALLINT(M)`
- Là 1 số nguyên ( ***integer*** ) nhỏ :
- Vùng `SIGNED` : từ `-32768` đến `32767`
- Vùng `UNSIGNED` : từ `0` đến `65535`
- Vùng `ZEROFILL`
### **1.4) INT**
- Cấu trúc : `INT(M)`
- Là 1 số nguyên bình thường .
- Vùng `SIGNED` ( mặc định ) : từ `-2147483648` đến `2147483647`
- Vùng `UNSIGNED` : từ `0` đến `4294967295`

### **1.5) FLOAT**
- Cấu trúc : `FLOAT(M,D)`
- Là 1 số thực nhỏ , với dấu "`,`" đứng ở giữa , trong đó `M` là số chữ số đằng trước dấu "`,`" , `D` là số chữ số đằng sau dấu "`,`"
- Dãy số `FLOAT` gồm :
    - `-3.402823466E+38` đến `-1.175494351E-38`
    - `0`
    - `1.175494351E-38` đến `3.402823466E+38`
- Vùng `UNSIGNED` là vùng không có giá trị âm .
### **1.6) DOUBLE**
- Cấu trúc : `DOUBLE(M,D)`
- Là 1 số thực thường , với dấu "`,`" đứng ở giữa , trong đó `M` là số chữ số đằng trước dấu "`,`" , `D` là số chữ số đằng sau dấu "`,`"
- Dãy số `DOUBLE` gồm :
    - `-1.7976931348623157E+308` đến `-2.2250738585072014E-308`
    - `0`
    - `2.2250738585072014E-308` đến `1.7976931348623157E+308`
- Vùng `UNSIGNED` là vùng không có giá trị âm .
## **2) STRING ( *chuỗi* )**
### **2.1) VARCHAR**
- Cấu trúc : `VARCHAR(M)`
- Là 1 chuỗi ( ***string*** ) với độ dài không cố định ( ***variable-length*** ) .
- Độ dài của chuỗi tối đa là `M` . `M` nằm trong khoảng từ `0` đến `65535` .
- Cột `VARCHAR(0)` có thể chứa ô trống hoặc ô có giá trị `NULL`
### **2.2) TEXT**
- Cấu trúc : `TEXT(M)`
- 1 cột `TEXT` sẽ có tối đa `65535` kí tự .
## **3) DATE AND TIME ( *thời gian* )**
### **3.1) DATE**
- Là giá trị ***ngày trong năm*** , được biểu diễn dưới dạng `YYYY-MM-DD` .
- Nằm trong khoảng `1000-01-01` đến `9999-12-31`
### **3.2) TIME**
- Là giá trị ***thời gian tính theo giờ*** , được biểu diễn dưới dạng `HH:MM:SS.sss` ( ***hour , minute , second , milisecond*** )
- Nằm trong khoảng `-838:59:59` đến `838:59:59`
### **3.3) DATETIME**
- Là giá trị gộp giữa `DATE` và `TIME` , được biểu diễn dưới dạng `YYYY-MM-DD HH:MM:SS`
- Nằm trong khoảng `1000-01-01 00:00:00` đến `9999-12-31 23:59:59`
### **3.4) TIMESTAMP**
- Là giá trị biểu diễn khoảng thời gian 1 dòng được thêm vào hoặc được update
- Nằm trong khoảng `1970-01-01 00:00:01` đến `2038-01-19 03:14:07`