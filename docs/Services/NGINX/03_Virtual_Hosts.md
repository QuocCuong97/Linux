# Cấu hình Virtual Host với NGINX
- **B1 :** Mở file `/etc/nginx/nginx.conf` và thêm vào tên miền chính của server :
    ```
    # vi /etc/nginx/nginx.conf
    ```
    - Thêm nội dung sau vào **HTTP block** :
        ```sh
        http {
            ...
            server {
                server_name     www.testing.com testing.com;
            }
            ...
        }
        ```
> Ở đây ta sẽ tạo 2 VirtualHost với địa chỉ là `testing1.com` và `testing2.com`
- **B2 :** Tạo các thư mục lưu trữ document root (code) của các VirtualHost :
    ```
    # mkdir /usr/share/nginx/testing1.com
    # chown nginx:nginx -R /usr/share/nginx/testing1.com
    --------------------------------------------------------
    # mkdir /usr/share/nginx/testing2.com
    # chown nginx:nginx -R /usr/share/nginx/testing2.com
    ```
- **B3 :** Thêm các file `index.html` chứa nội dung web của các VirtualHost :
    ```
    # vi /usr/share/nginx/testing1.com/index.html
    ```
    - Thêm vào nội dung sau :   
        ```html
        <!DOCTYPE html>
        <html>
          <head>
            <title>testing1.com</title>
          </head>
          <body>
            <h1 align=center>Welcome to testing1.com</h1>
          </body>
        </html>
        ```
    ```
    # vi /usr/share/nginx/testing2.com/index.html
    ```
    - Thêm vào nội dung sau :   
        ```html
        <!DOCTYPE html>
        <html>
          <head>
            <title>testing2.com</title>
          </head>
          <body>
            <h1 align=center>Welcome to testing2.com</h1>
          </body>
        </html>
        ```
- **B4 :** Tạo các file VirtualHost trong thư mục `/etc/nginx/conf.d/` :
    ```
    # vi /etc/nginx/conf.d/testing1.com.conf
    ```
    - Thêm vào nội dung sau :
        ```
        server {
            listen      80;
            server_name     testing1.com www.testing1.com;
            access_log      /var/log/nginx/access-testing1.com.log;
            error_log       /var/log/nginx/error-testing1.com.log;
            root    /usr/share/nginx/testing1.com;
            index   index.php index.html index.htm;
        }
        ```
    ```
    # vi /etc/nginx/conf.d/testing2.com.conf
    ```
    - Thêm vào nội dung sau :
        ```
        server {
            listen      80;
            server_name     testing2.com www.testing2.com;
            access_log      /var/log/nginx/access-testing2.com.log;
            error_log       /var/log/nginx/error-testing2.com.log;
            root    /usr/share/nginx/testing2.com;
            index   index.php index.html index.htm;
        }
        ```
- **B5 :** Phân giải tên miền . Nếu không có máy chủ DNS thì có thể thêm nội dung sau vào file host của Client Windows theo đường dẫn  "`C:\Windows\System32\drivers\etc/hosts`" :
    ```
    192.168.5.10   www.testing.com testing.com testing1.com www.testing1.com www.testing2.com testing2.com
    ```
- **B6 :** Kiểm tra trên phía trình duyệt của client : 
    ```
    http://testing.com
    ```
    <img src=https://i.imgur.com/5xaicUW.png>

    ```
    http://testing1.com
    ```
    <img src=https://i.imgur.com/qrzKeNE.png>

    ```
    http://testing2.com
    ```
    <img src=https://i.imgur.com/f3FLtLZ.png>
