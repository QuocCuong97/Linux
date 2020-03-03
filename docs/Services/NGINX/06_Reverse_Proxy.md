# Cấu hình Reverse Proxy
## **Mô hình**
<img src=https://i.imgur.com/Koe2EoS.png>

## **Cấu hình NGINX Reverse Proxy**
- **B1 :** Khởi tạo repo **NGINX** :
    ```
    # vi /etc/yum.repos.d/nginx.repo
    ```
    - Thêm vào nội dung sau :
    ```
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/7/$basearch/
    gpgcheck=1
    ```
- **B2 :** Import chữ ký xác thực :
    ```
    # wget --no-check-certificate -O nginx_signing.key https://nginx.org/keys/nginx_signing.key
    # rpm --import nginx_signing.key
    ```
- **B3 :** Cập nhật thông tin repo :
    ```
    # yum repolist
    ```
- **B4 :** Cài đặt **NGINX** thông qua repo trước đó :
    ```
    # yum --disablerepo=* --enablerepo=nginx install nginx -y
    ```
- **B5 :** Cấu hình **firewalld** cho phép port 80 :
    ```
    # firewall-cmd --zone=public --permanent --add-port=80/tcp
    # firewall-cmd --zone=public --permanent --add-port=443/tcp
    # firewall-cmd --reload
    ```
- **B6 :** Khởi động và cấu hình startup cho dịch vụ :
    ```
    # systemctl start nginx.service
    # systemctl enable nginx.service
    ```
- **B7 :** Sửa file cấu hình `/etc/nginx/nginx.conf` :
    ```
    # vi /etc/nginx/nginx.conf
    ```
    - Thêm vào đoạn sau :
        ```sh
        http {
            ...

            server {
                server_name     www.testing.com testing.com;
                listen      80 default_server;
                listen      [::]:80 default_server;

                proxy_redirect           off;
                proxy_set_header         X-Real-IP $remote_addr;
                proxy_set_header         X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header         Host $http_host;

                location / {
                    proxy_pass http://192.168.5.20/;
                }

            }
            ...
        }

        # trong đó 192.168.5.20 là địa chỉ của Web Server Backend
        ```
- **B8 :** Khởi động lại dịch vụ **NGINX** :
    ```
    # systemctl restart nginx
    ```
## **Cấu hình Web Server Apache (Backend)**
- **B1 :** Cài đặt `httpd` :
    ```
    # yum -y install httpd
    ```
- **B2 :** Cấu hình **firewalld** cho phép port `80` và `443` :
    ```
    # firewall-cmd --zone=public --permanent --add-port=80/tcp
    # firewall-cmd --zone=public --permanent --add-port=443/tcp
    # firewall-cmd --reload
    ```
- **B3 :** Khởi động và cấu hình startup cho dịch vụ `httpd` :
    ```
    # systemctl start httpd
    # systemctl enable httpd
    ```
- **B4 :** Chỉnh sửa lại format ghi log trong file cấu hình `/etc/httpd/conf/httpd.conf` :
    ```
    # vi /etc/httpd/conf/httpd.conf
    ```
    Sửa lại tại dòng `196` :
    ```
    LogFormat "\"%{X-Forwarded-For}i\" %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined 
    ```
    <img src=https://i.imgur.com/F48VlqE.png>

- **B5 :** Khởi động lại dịch vụ `httpd` :
    ```
    # systemctl restart httpd
    ```
## **Kiểm tra kết quả**
- Truy cập vào địa chỉ của **NGINX Reverse Proxy** trên trình duyệt của client và nó sẽ điều hướng đến **Web Server Apache** :
    ```
    http://testing.com
    ```
    <img src=https://i.imgur.com/vrO8Ske.png>