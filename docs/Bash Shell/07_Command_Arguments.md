# Tham số dòng lệnh trong Bash
- **Command line arguments** hay *tham số dòng lệnh* là những tham số có chức năng tương tự biến hệ thống , rất hữu ích để làm script linh hoạt hơn .
- Bảng **tham số dòng lệnh** :

    | **Special Variable** | **Variable Details** |
    |----------------------|----------------------|
    | `$1` -> `$n` | `$1` là biến thứ nhất , `$2` là biến thứ 2 cho đến biến thứ `n` . Từ biến thứ `10` phải đi kèm dấu `{}` : `${10}` , `${11}` ,.... |
    | `$0` | Tên của chính file script |
    | `$$` | Process ID của shell hiện tại |
    | `$*` | Giá trị của tất cả các tham số . Tất cả các tham số được ký hiệu là `"$*"` |
    | `$#` | Tổng số các tham số chạy qua script |
    | `$@` | Giá trị của tất cả các tham số |
    | `$?` | Exit Status Code của lệnh vừa thực hiện |
    | `$!` | Process ID của lệnh gần nhất vừa thực hiện |

- **VD :**
    ```bash
    #!/bin/bash
    
    ### Print total arguments and their values
    
    echo "Total Arguments:" $#
    echo "All Arguments values:" $@
    
    ### Command arguments can be accessed as
    
    echo "First->"  $1
    echo "Second->" $2
    
    # You can also access all arguments in an array and use them in a script.
    
    args=("$@")
    echo "First->"  ${args[0]} 
    echo "Second->" ${args[1]}
    ```
    Chạy Script . Cho vào 2 tham số `Hello` và `World` :
    ```
    # chmod +x test.sh
    # ./test.sh Hello World
    ```
    => Output :
    ```
    Total Arguments: 2
    All Arguments values: Hello World
    First-> Hello
    Second-> World
    First-> Hello
    Second-> World
    ```
