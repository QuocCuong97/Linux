# Sử dụng Webvirt Manager để quản lý các VMs
## **1) Cài đặt Webvirt Manager trên CentOS 7**
- **B1 :** Update các package sẵn có :
    ```
    # yum -y update
    ```
- **B2 :** Cài đặt các package cần thiết :
    ```
    # yum install -y epel-release
    # yum install -y python-virtualenv python-devel libvirt-devel glibc gcc nginx supervisor python-lxml git python-libguestfs
    ```
- **B3 :** Clone source code `webvirtcloud` :
    ```
    # cd /srv
    # git clone https://github.com/retspen/webvirtcloud
    # cd webvirtcloud
    ```
- **B4 :** Copy file `settings.py` ra ngoài để sử dụng :
    ```
    # cp webvirtcloud/settings.py.template webvirtcloud/settings.py
    ```
- **B5 :** Trong file `settings.py` , thay đổi `SECRET_KEY` thành một chuỗi string riêng :
    ```
    # vi webvirtcloud/settings.py
    ```
    <img src=https://i.imgur.com/afY4Z2v.png>

- **B6 :** Cài đặt `virtualenv` ( đứng tại thư mục `/srv/webvirtcloud` ):
    ```
    # virtualenv env
    # source env/bin/activate
    # pip install -r conf/requirements.txt
    # python manage.py migrate
    ```
- **B7 :** Copy file cấu hình `webvirtcloud.conf` sang thư mục `/etc/nginx/conf.d/` :
    ```
    # cp conf/nginx/webvirtcloud.conf /etc/nginx/conf.d/
    ```
- **B8 :** Backup file cấu hình cho **supervisor** :
    ```
    # cp /etc/supervisord.conf /etc/supervisord.conf.bak
    ```
- **B9 :** Chỉnh sửa file cấu hình của **supervisor** :
    ```
    # vi /etc/supervisord.conf
    ```
    - Thêm vào cuối file đoạn sau :
        ```
        [program:webvirtcloud]
        command=/srv/webvirtcloud/env/bin/gunicorn webvirtcloud.wsgi:application -c /srv/webvirtcloud/gunicorn.conf.py
        directory=/srv/webvirtcloud
        user=nginx
        autostart=true
        autorestart=true
        redirect_stderr=true

        [program:novncd]
        command=/srv/webvirtcloud/env/bin/python /srv/webvirtcloud/console/novncd
        directory=/srv/webvirtcloud
        user=nginx
        autostart=true
        autorestart=true
        redirect_stderr=true
        ```
- **B10 :** Chỉnh sửa file cấu hình **nginx** : 
    ```
    # vi /etc/nginx/nginx.conf 
    ```
    - Comment toàn bộ block server :
        
        <img src=https://i.imgur.com/MWH7kVJ.png>

- **B11 :** Chỉnh sửa file cấu hình `webvirtcloud.conf` :
    ```
    # vi /etc/nginx/conf.d/webvirtcloud.conf
    ```
    - Chỉnh sửa như sau :
        ```
        upstream gunicorn_server {
            #server unix:/srv/webvirtcloud/env/wvcloud.socket fail_timeout=0;
            server 127.0.0.1:8000 fail_timeout=0;
        }
        server {
            listen 80;

            server_name servername.domain.com;
            access_log /var/log/nginx/webvirtcloud-access_log; 

            location /static/ {
                root /srv/webvirtcloud;
                expires max;
            }

            location / {
                proxy_pass http://gunicorn_server;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-for $proxy_add_x_forwarded_for;
                proxy_set_header Host $host:$server_port;
                proxy_set_header X-Forwarded-Proto $remote_addr;
                proxy_connect_timeout 600;
                proxy_read_timeout 600;
                proxy_send_timeout 600;
                client_max_body_size 1024M;
            }
        }
        ```
- **B12 :** Phân quyền cho các thư mục :
    ```
    # chown -R nginx:nginx /srv/webvirtcloud
    # yum install policycoreutils-python -y
    # setenforce 0
    # semanage fcontext -a -t httpd_sys_content_t "/srv/webvirtcloud(/.*)"
    ```
- **B13 :** Cấu hình **firewalld** :
    ```
    # firewall-cmd --permanent --add-port=80/tcp
    # firewall-cmd --permanent --add-port=6080/tcp
    # firewall-cmd --reload
    ```
- **B14 :** Restart lại các dịch vụ và cho phép chúng khởi động cùng hệ thống :
    ```
    # systemctl restart nginx && systemctl restart supervisord
    # systemctl enable nginx && systemctl enable supervisord
    ```
- **B15 :** Trên trình duyệt của client , truy cập địa chỉ của **WebvirtCloud**, login với user mặc định : `admin/admin` :
    ```
    http://192.168.5.10
    ```
    <img src=https://i.imgur.com/TW97ILb.png>

- **B16 :** Chọn tab **Computes** :

    <img src=https://i.imgur.com/LHF0dcV.png>

- **B17 :** Chọn biểu tượng dấu "`+`" để thêm **compute** :

    <img src=https://i.imgur.com/XVKJFud.png>