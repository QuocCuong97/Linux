# Script kiểm tra thuộc tính của file
- Các thuộc tính của file :
    | Thuộc tính | Kết quả |
    |------------|---------|
    | `-e file` | `true` nếu file tồn tại |
    | `-f file` | `true` nếu file là tập tin thông thường |
    | `-d file` | `true` nếu file là thư mục |
    | `-s file` | `true` nếu file_size khác 0 |
    | `-u file` | `true` nếu file được thiết lập **`SUID`** |
    | `-g file` | `true` nếu file được thiết lập **`SGID`** |
    | `-k file` | `true` nếu file được thiết lập **`Sticky Bit`** |
    | `-r file` | `true` nếu file cho phép quyền `read` |
    | `-w file` | `true` nếu file cho phép quyền `write` |
    | `-x file` | `true` nếu file cho phép quyền `execute` |

## **Sử dụng lệnh `test`**
- Cấu trúc :
    ```
    # test dieu_kien
    ```
    hoặc 
    ```
    # [dieu_kien]
    ```
## **Sử dụng `bash`**
- **VD :**
    - Kiểm tra quyền `read` của file :
        ```bash
        #!/bin/bash
        
        if [ -r /tmp/myfile.txt ]
        then
            echo "File has read permission"
        else
            echo "You don't have read permission"
        fi
        ```
    - Kiểm tra quyền `write` của file :
        ```bash
        #!/bin/bash
        
        if [ -w /tmp/myfile.txt ]
        then
            echo "File has write permission"
        else
            echo "You don't have write permission"
        fi
        ```
    - Kiểm tra quyền `execute` của file :
        ```bash
        #!/bin/bash
        
        if [ -x /tmp/myfile.txt ]
        then
            echo "File has execute permission"
        else
            echo "You don't have execute permission"
        fi
        ```