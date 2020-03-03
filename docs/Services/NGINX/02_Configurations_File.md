# Ý nghĩa file cấu hình chính
- File cấu hình chính của **NGINX** là `/etc/nginx/nginx.conf`
    ```
    # cat /etc/nginx/nginx.conf
    ```
    ```sh
        
    user  nginx;
    worker_processes  1;

    error_log  /var/log/nginx/error.log warn;
    pid        /var/run/nginx.pid;


    events {
        worker_connections  1024;
    }


    http {
        include       /etc/nginx/mime.types;
        default_type  application/octet-stream;

        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                        '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for"';

        access_log  /var/log/nginx/access.log  main;

        sendfile        on;
        #tcp_nopush     on;

        keepalive_timeout  65;

        #gzip  on;

        include /etc/nginx/conf.d/*.conf;
    }
    ```
- File cấu hình của **NGINX** chia thành các **directives** thường và **block directive** . 
    - Một **directives** thường bao gồm tên và các tham số được phân cách bởi các khoảng trắng và kết thúc bằng dấu chấm phẩy (`;`)
    - Các **directives** không thuộc một block thì được coi là nằm trong **main block** - phần sẽ ảnh hưởng đến toàn server .
        ```sh
        user  nginx;
        worker_processes  1;

        error_log  /var/log/nginx/error.log warn;
        pid        /var/run/nginx.pid;
        ```
        - "`user  nginx;`" : cấu hình quy định **worker process** sẽ chạy với user nào . Ở đây là user `nginx` .
        - "`worker_processes  1;`" : cấu hình quy định số CPU core được sử dụng cho **worker process** . Ở đây là `1` core . Xem số core của server bằng lệnh "`cat /proc/cpuinfo`" hoặc "`nproc`"
        - "`error_log  /var/log/nginx/error.log warn;`" : đường dẫn đến thư mục log của **NGINX** :
        - "`pid        /var/run/nginx.pid;`" : đường dẫn đến số PID của master process , sử dụng để quản lý **worker process** .

    - Một **block directives** có cấu trúc tương tự với **directives** thường, nhưng thay vì kết thúc bằng dấu chấm phẩy (`;`) nó lại kết thúc bằng một bộ các công thức kèm theo được chứa trong các dấu ngoặc `({` và `})` . Một **block directives** có thể chứa các **directives** thường bên trong nó .
        ```sh
        events {
            worker_connections  1024;
        }


        http {
            include       /etc/nginx/mime.types;
            default_type  application/octet-stream;

            log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                            '$status $body_bytes_sent "$http_referer" '
                            '"$http_user_agent" "$http_x_forwarded_for"';

            access_log  /var/log/nginx/access.log  main;

            sendfile        on;
            #tcp_nopush     on;

            keepalive_timeout  65;

            #gzip  on;

            include /etc/nginx/conf.d/*.conf;
        }
        ```
        - **Events block** :
            - "`worker_connections  1024;`" : số kết nối mà 1 **worker process** có thể chịu tải đồng thời cùng một lúc . Thông số này có thể thay đổi tùy vào phần cứng của server , không phải mặc định .
        - **HTTP block** :
            - `log_format  main  '$remote_addr - $remote_user ....` : định dạng của các dòng log httpd
            - `access_log  /var/log/nginx/access.log  main;` : đường dẫn log truy cập
            - `keepalive_timeout  65;` : thời gian chờ khi đóng một kết nối

