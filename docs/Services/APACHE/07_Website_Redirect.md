# Cấu hình Website Redirect ( `mod_rewrite` )
## **1) `mod_rewrite` là gì**
- `mod_rewrite` là một module mở rộng của **Apache** , cho phép bạn viết lại URL . Chức năng chính của nó là ánh xạ một địa chỉ URL đến một vị trí file cụ thể của Server, tuy nhiên bạn có thể làm nhiều hơn thế như chuyển hướng, chặn truy cập ... 
- `mod_rewrite` mạnh và mềm dẻo , có thể đưa ra số quy tắc không hạn chế để biến đổi URL , mỗi quy tắc đó cũng hoạt động dựa vào số điều kiện bạn ra một cách không hạn chế .
- `mod_rewrite` cung cấp các chỉ thị để bạn sử dụng như `RewriteBase` , `RewriteCond` , `RewriteRule` ... để bạn viết nó trong `httpd.conf` hoặc trong file `.htaccess` :
    - Nếu chọn viết trong `.htaccess` , thì các `Rewrite` có hiệu lực trên thư mục đặt file đó . Khi **Apache** truy cập đến bất kỳ thư mục con nào thì nó cũng tìm xem trong thư mục đó có file `.htaccess` không để nạp vào . Viết cách này khá linh hoạt , nhưng đôi khi làm chậm một chút , nếu có thể ở cấu hình VirtualHost tắt hẳn `.htaccess` bằng chỉ thị `AllowOverride None` để chỉ viết trong cấu hình Apache `httpd.conf` .
    - Các quy tắc `ReWrite` viết trong `httpd.conf` ở các `Directory` của VirtualHost, kết quả thì cũng tương tự viết trong `.htaccess` , nó nhanh hơn vì các luật `ReWrite` Apache không phải tìm vào nạp mỗi khi truy cập , viết xong phải khởi động lại Apache để có hiệu lực .
## **3) Cấu hình**
- **B1:** Kiểm tra `mod_rewrite` đã được nạp chưa :
    ```
    httpd -M
    ```
    <img src=https://i.imgur.com/xe0LdPS.png>

    - Nếu `mod_rewrite` chưa được nạp , tiến hành nạp `mod_rewrite` :
        ```
        # vi /etc/httpd/conf.modules.d/00-base.conf
        ```
        Thêm vào nội dung sau :

        ```
        LoadModule rewrite_module modules/mod_rewrite.so
        ```
        Khởi động lại dịch vụ để **Apache** nhận module mới :
        ```
        # systemctl restart httpd
        ```
- **B2 :** Update cài đặt `AllowOverride` để cho phép việc ghi đè lệnh trên **Apache** :
    ```
    # vi /etc/httpd/conf/httpd.conf
    :set nu
    ```
    - Tại dòng `151` , sửa `None` thành `All` :

        <img src=https://i.imgur.com/ZucjU0O.png>

    - Khởi động lại dịch vụ :
        ```
        # systemctl restart httpd
        ```
- **B2 :** Thiết lập một file `.htaccess` :
    - Một file `.htaccess` cho phép định nghĩa một lệnh chỉ thị cho **Apache** , bao gồm một `RewriteRule` , trên một per-domain basis mà không ảnh hưởng tới các file cấu hình của hệ thống . Trong Linux , những file này được đặt sau một dấu (`.`) và bị ẩn .
    - Tạo một file .htaccess ở thư mục gốc `/var/www/html` :
        ```
        # vi /var/www/html/.htaccess
        ```
        - Thêm vào nội dung sau :
            ```
            RewriteEngine On
            ```
- **B3 :** Tạo 1 `RewriteRule` :
    - Cú pháp cuả một lệnh `RewriteRule` : lệnh `RewriteRule` cho phép ta chuyển các yêu càu đến **Apache** dựa trên URL. Một fiel.htaccess có thể chứa một hoặc nhiều hơn một luật rewrite, nhưng tại run-time Apache chỉ áp dụng những luật đó theo thứ tự của chúng trong file . Cấu trúc lệnh `rewrite` :
        ```
        RewriteRule Pattern Substitution [Flags]
        ```
        - `RewriteRule` : chỉ rõ lệnh `RewriteRule`
        - `Pattern` : Một **PCRE (Perl Compatible Regular Expression)** khớp với chuỗi mong muốn .
        - `Substitution` : nơi yêu cầu ghép được gửi 
        - `[Flags]` : thông số bổ sung để thay đổi luật

