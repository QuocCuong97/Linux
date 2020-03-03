# Cấu hình Basic Authentication
- **B1 :** Cài đặt `httpd-tools` :
    ```
    # yum -y install httpd-tools 
    ```
- **B2 :** Chỉnh sửa file `/etc/nginx/nginx.conf` : thêm đoạn code sau vào **server block** trong **HTTP block** :
    ```sh
    http {
        ...
        
        server {
            root   /usr/share/nginx/html;
            server_name     www.testing.com testing.com;
            location / {
                auth_basic            "Basic Auth";
                auth_basic_user_file  "/etc/nginx/.htpasswd";
            }
        }
        ...
    }
    ```
    > Trong đó, `/auth` là folder sử dụng để khi client truy cập vào folder đó cần phải xác thực mới xem được nội dung web .
- **B3 :** Tạo tài khoản và mật khẩu để xác thực cho trang web :
    ```
    # htpasswd -c /etc/nginx/.htpasswd admin
    ```
    <img src=https://i.imgur.com/ArSWGNg.png>

    > `admin` ở đây là tên tài khoản . Có thể tạo tài khoản khác .
- **B4 :** Khởi động lại dịch vụ **NGINX** :
    ```
    # systemctl restart nginx
    ```
- **B5 :** Truy cập trình duyệt trên client để kiểm tra :
    ```
    http://testing.com
    ```
    <img src=https://i.imgur.com/gvDgs2j.png>

    Sau khi xác thực thành công có thể vào được trang web :
    
    <img src=https://i.imgur.com/WIzeYfL.png>