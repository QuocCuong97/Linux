# Shell Scripting
## **1) Shell script là gì ?**
- **Shell** là chương trình giao tiếp với người dùng , chấp nhận các lệnh nhập từ keyboard và thực thi nó .
- Nếu muốn sử dụng nhiều lệnh chỉ bằng 1 lệnh , có thể lưu chuỗi lệnh vào file text và bảo **shell** thực thi chuỗi lệnh thay vì tự nhập vào các lệnh .
- **Shell-script** là 1 chuỗi các lệnh được viết trong *plain-text* file ( giống **batch** trong MS-DOS nhưng mạnh mẽ hơn ) .
- Ưu điểm :
    - **Shell-script** có thể nhận input từ user , file hoặc output từ màn hình .
    - Tiện lợi để tạo nhóm lệnh riêng 
    - Tiết kiệm thời gian
    - Tự động làm các công việc đã được lên lịch
## **2) Cách tạo và thực thi shell-script**
- **B1 :** Tạo file `shell.sh`
    - Sử dụng `vi` , `emacs` , `gedit` ,... để tạo nội dung cho file :
    ```
    #!/bin/bash
    echo "hello world"
    ```
    - Dòng đầu tiên luôn là "`#!/bin/bash`" . Đây là cú pháp bắt buộc .
    - Sau "`#`" được hiểu là comment - chú thích của các đoạn code .
- **B2 :** Cấp quyền **execute** cho file :
    ```
    # chmod 755 shell.sh
    ```
    hoặc
    ```
    # chmod 0777 shell.sh
    ```
- **B3 :** Thực thi file shell :
    ```
    # ./shell.sh
    ```
    hoặc
    ```
    # bash shell.sh
    ```
    hoặc
    ```
    # sh shell.sh
    ```