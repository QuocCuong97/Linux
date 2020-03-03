# Cấu hình trang trạng thái NGINX
- **B1 :** Kiểm tra **NGINX** đã được load module `HTTPSubStatus` chưa :
    ```
    # nginx -V 2>&1 | grep -o with-http_stub_status_module
    with-http_stub_status_module             (đã cài đặt)
    ```
    - Nếu chưa được load module này , cần compile lại **NGINX** :
        ```
        # ./configure ... .... --with-http_stub_status_module
        # make & make install
        ```
- **B2 :** Chỉnh sửa file `/etc/nginx/nginx.conf` : thêm đoạn code sau vào **server block** trong **HTTP block** :
    ```
    http {
        ...
        
        server {
            root   /usr/share/nginx/html;
            server_name     www.testing.com testing.com;
            .......
            location /status {
                stub_status on;
                access_log off;
                allow all;
            }
        }
        ...
    }
    ```
    > Có thể thay đổi đường dẫn `/status` thành đường dẫn gì tùy ý . Chức năng "`allow all;`" cho phép tất cả các IP có thể truy cập trang status này . Có thể giới hạn IP bằng chức năng "`deny [IP];`"
- **B3 :** Truy cập trình duyệt trên client để kiểm tra :
    ```
    http://testing.com/status
    ```
    <img src=https://i.imgur.com/ClReTEl.png>

    hoặc cũng có thể kiểm tra local bằng lệnh `curl` :
    
    ```
    # curl -X GET http://127.0.0.1/status  
    Active connections: 1
    server accepts handled requests
    3 3 2
    Reading: 0 Writing: 1 Waiting: 0
    ```
> ### **Trong đó :**
- **Active connections** : số lượng tất cả các kết nối đang mở 
- **server accepts handled requests** : 
    - Tham số thứ nhất : **Accepted connections** : số lượng kết nối đã tiếp nhận
    - Tham số thứ hai : **Handled connections** : số lượng kết nối đã xử lý ( thường bằng với tham số thứ nhất )
    - Tham số thứ ba : **Handled requests** : số lượng request được xử lý 
- **Reading** : **nginx** đang đọc request header
- **Writing** : **nginx** đọc request body , xử lý request hoặc đang gửi response cho client 
- **Waiting** : các kết nối keep-alived
