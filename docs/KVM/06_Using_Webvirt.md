# Sử dụng Webvirt Manager để quản lý các VMs
## **1) Cài đặt Webvirt Manager trên CentOS 7**
### **Mô hình**
<img src=https://i.imgur.com/vRKA3Sf.png>

### **Cài đặt KVM Computes**
- **B1 :** Trên máy **KVM Computes** , cài đặt KVM để quản lý qua **Webvirt Cloud**<br>[Hướng dẫn cài đặt KVM trên CentOS 7](https://github.com/QuocCuong97/Linux/blob/master/docs/KVM/03_Install_KVM.md#1-c%C3%A0i-%C4%91%E1%BA%B7t-tr%C3%AAn-centos-7)
- **B2 :** Chỉnh sửa file cấu hình `libvirt` :
    ```
    # vi /etc/libvirt/libvirtd.conf
    ```
    - Chỉnh sửa nội dung như sau :
        ```
        listen_tls = 0
        listen_tcp = 1
        tcp_port = "16509"
        listen_addr = "0.0.0.0"
        auth_tcp = "none"  
        ```
- **B3 :** Chỉnh sửa file `/etc/sysconfig/libvirtd` :

    <img src=https://i.imgur.com/FazKO1M.png>
- **B4 :** Cho phép port `16509` của **libvirt** và dải port `5900-5999` của **VNC** đi qua **firewalld** :
    ```
    # firewall-cmd --permanent --add-port=16509/tcp
    # firewall-cmd --permanent --add-port=5900-5999/tcp
    # firewall-cmd --reload
    ```
- **B5 :** Restart lại dịch vụ `libvirt` :
    ```
    # systemctl restart libvirtd
    ```
### **Cài đặt Webvirt Cloud**
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
    http://192.168.5.20
    ```
    <p align=center><img src=https://i.imgur.com/2AmqGzY.png width=80%></p>

- **B16 :** Chọn tab **Computes** :

    <p align=center><img src=https://i.imgur.com/BcelVEG.png width=80%></p>

- **B17 :** Chọn biểu tượng dấu "`+`" để thêm **compute** :

    <p align=center><img src=https://i.imgur.com/XcbONWl.png width=80%></p>

- **B18 :** Nhập các thông tin để liên kết đến **KVM Computes** :

    <p align=center><img src=https://i.imgur.com/lSDIy6Y.png width=80%></p>

    => Kết nối thành công :

    <p align=center><img src=https://i.imgur.com/6rn96P5.png width=80%></p>

    => Trong tab **Instances** sẽ xuất hiện máy ảo đã tạo trong **KVM Computes** :

    <p align=center><img src=https://i.imgur.com/G702Tma.png width=80%></p>

## **2) Cài đặt Webvirt Manager trên Ubuntu**
## **3) Sử dụng Webvirt Cloud**
### **3.1) Thay đổi password user `admin`** 
- **B1 :** Vào tab **Users**, chọn vào biểu tượng hình bánh răng ở ô của user `admin` :

    <p align=center><img src=https://i.imgur.com/POS3asb.png width=80%></p>

- **B2 :** Nhập password muốn đổi và các trường bắt buộc phía dưới, chọn ***Edit*** :

    <p align=center><img src=https://i.imgur.com/60uRbJ3.png width=80%></p>

- **B3 ;** Sau khi chọn ***Edit***, hệ thống sẽ tự động logout và yêu cầu người dùng đăng nhập bằng mật khẩu mới .
### **3.2) Tạo user mới**
- **B1 :** Trong tab **Users**, chọn vào biểu tượng dấu "`+`" ở góc phải màn hình :

    <p align=center><img src=https://i.imgur.com/3Uavr8A.png width=80%></p>

- **B2 :** Tại cửa sổ **Add New User**, nhập username muốn tạo và passwword, sau đó chọn ***Create*** :

    <p align=center><img src=https://i.imgur.com/edV2y3U.png width=80%></p>

- **B3 :** Chọn vào biểu tượng hình bánh răng ở ô của user `cuongnq` :

    <p align=center><img src=https://i.imgur.com/HpFMcuj.png width=80%></p>

- **B4 :** Tại cửa sổ **Edit user info**, nhập các thông tin cần thiết ở các trường, sau đó chọn ***Edit*** :

    <p align=center><img src=https://i.imgur.com/L0Z3ADf.png width=80%></p>

### **3.3) Tạo Storage Pool**
- Tạo **Storage Pool** để lưu trữ các volume của VMs và các file `.iso` để cài đặt OS cho VMs
- **B1 :** Trong tab **Computes**, chọn vào cụm KVM muốn tạo **Storage Pool** :

    <p align=center><img src=https://i.imgur.com/run5A5V.png width=80%></p>

- **B2 :** Chọn tab **Storages** :

    <p align=center><img src=https://i.imgur.com/DrvRpYk.png width=80%></p>

- **B3 :** Click vào biểu tượng dấu "`+`" ở góc phải màn hình thể thêm pool mới :

    <p align=center><img src=https://i.imgur.com/xl8zAIJ.png width=80%></p>

- **B4 :** Tại cửa sổ **Create Storage Pool**, chọn tab **DIR** để tạo pool chứa các volumes VMs :

    <p align=center><img src=https://i.imgur.com/5p6E5gf.png width=80%></p>

- **B5 :** Chọn tab **ISO** để tạo pool chứa các file `.iso` cho quá trình cài đặt OS :

    <p align=center><img src=https://i.imgur.com/C1ci82l.png width=80%></p>

    => Kết quả :

    <p align=center><img src=https://i.imgur.com/QcvMXbO.png width=80%></p>

### **3.4) Tạo Networks Pool**
- **B1 :** Trong tab **Networks**, chọn biểu tượng dấu "`+`" ở góc phải màn hình :

    <p align=center><img src=https://i.imgur.com/whLEoku.png width=80%></p>

- **B2 :** Trong cửa sổ **Add New Network**, chọn loại card mạng cần tạo và chọn ***Create*** :

    <p align=center><img src=https://i.imgur.com/sidj4MP.png width=80%></p>

### **3.5) Gán VMs cho user** :
- **B1 :** Trong tab **Users**, chọn vào user muốn gán VMs :

    <p align=center><img src=https://i.imgur.com/ADFfOHV.png width=80%></p>

- **B2 :** Chọn vào biểu tượng dấu "`+`" ở góc phải màn hình :

    <p align=center><img src=https://i.imgur.com/R8pCrhB.png width=80%></p> 

- **B3 :** Tại cửa sổ **Add Instance for User**, chọn host muốn gán cho user và chọn ***Add*** :

    <p align=center><img src=https://i.imgur.com/GQxCG14.png width=80%></p>

- **B4 :** Sau khi gán xong, mặc định các quyền của user tạm thời chưa sử dụng được vì bị set là `False` . Chọn biểu tượng `Edit` :

    <p align=center><img src=https://i.imgur.com/ua7EKqB.png width=80%></p>

- **B5 :** Chỉnh sửa các quyền cho phù hợp rồi chọn ***Edit*** :

    <p align=center><img src=https://i.imgur.com/m5BVqz6.png width=80%></p>

    => Kết quả :

    <p align=center><img src=https://i.imgur.com/kvvKZVc.png width=80%></p>

- **B6 :** Login bằng user vừa được gán quyền. Máy vừa gán sẽ hiện trên tab **Instances** :

    <p align=center><img src=https://i.imgur.com/9JtwJrj.png width=80%></p>

### **3.6) Tạo VMs**
> Để tạo được VMs, phải đăng nhập dưới quyền **ADMIN**!
- **B1 :** Trong tab **Instances**, chọn biểu tượng dấu "`+`" ở góc phải màn hình :

    <p align=center><img src=https://i.imgur.com/0XM0SZw.png width=80%></p>

- **B2 :** Chọn cụm Computes đã liên kết :

    <p align=center><img src=https://i.imgur.com/aMiRT13.png width=80%></p>

- **B3 :** Tại cửa sổ **Architecture**, để thông tin mặc định và chọn ***Next*** :

    <p align=center><img src=https://i.imgur.com/N0E1h9R.png width=80%></p>

- **B4 :** Tại cửa sổ tiếp theo, có 3 tùy chọn :
    - **Flavor** : Cài theo cấu hình gợi ý theo từng level
    - **Custom** : Cài theo cách tự tùy chỉnh hoàn toàn các thông số
    - **Template** : Cài bằng template có sẵn :

    <p align=center><img src="https://i.imgur.com/mXxRBzB.png" width=80%></p>

> <h3 align=center><strong>1 - Cài theo Flavor</strong></h3>
- **B5 :** Chọn biểu tượng dấu "`+`" ở cấu hình flavor muốn cài :

    <p align=center><img src=https://i.imgur.com/6OEfNNn.png width=80%></p>

- **B6 :** Nhập đầy đủ các thông tin về VM muốn tạo và chọn ***Create*** :

    <p align=center><img src=https://i.imgur.com/5tqacx9.png width=80%></p>

    => VM được tạo thành công :

    <p align=center><img src=https://i.imgur.com/1vLKf2U.png width=80%></p>

- **B7 :** Trong tab **Settings**, chọn tab **Disk**, tiến hành chọn file `.iso` mà mount vào VM vừa cài đặt :

    <p align=center><img src=https://i.imgur.com/Bepr8CW.png width=80%></p>

- **B8 :** Trong tab **Power**, chọn ***Power on*** để bật máy ảo :

    <p align=center><img src=https://i.imgur.com/57zyRqI.png width=80%></p>

- **B9 :** Sau khi đã bật máy, trong tab **Access**, chọn ***Console*** để truy cập giao diện VNC điều khiển VM :

    <p align=center><img src=https://i.imgur.com/Wm7rm8r.png width=80%></p>

    => Kết quả : Giao diện console bằng VNC :

    <p align=center><img src=https://i.imgur.com/wNtUS7M.png width=80%></p>

> <h3 align=center><strong>2 - Cài theo Custom</strong></h3>
- **B5 :** Trong tab **Computes**, chọn cụm KVM, vào tab **Storages** , chọn pool lưu trữ volume để tạo mới volumes :

    <p align=center><img src=https://i.imgur.com/1kr4qOA.png width=80%></p>

- **B6 :** Chọn dấu "`+`" ở góc phải màn hình để tạo volume mới :

    <p align=center><img src=https://i.imgur.com/TWR27y8.png width=80%></p>

- **B7 :** Tại cửa sổ **Add New Volume** , nhập các thông tin cần thiết cho volume, sau đó chọn ***Create*** :

    <p align=center><img src=https://i.imgur.com/TJUzBSM.png width=80%></p>

    => Kết quả :

    <p align=center><img src=https://i.imgur.com/E5jWi6f.png width=80%></p>

- **B8 :** Tại tab **Custom**, nhập đầy đủ các thông tin về VM muốn tạo và chọn ***Create*** :

    <p align=center><img src=https://i.imgur.com/yfsnqrz.png width=80%></p>

    => VM được tạo thành công :

    <p align=center><img src=https://i.imgur.com/ivLO9ec.png width=80%></p>

- **B9 :** Trong tab **Settings**, chọn tab **Disk**, tiến hành chọn file `.iso` mà mount vào VM vừa cài đặt :

    <p align=center><img src=https://i.imgur.com/Bepr8CW.png width=80%></p>

- **B10 :** Trong tab **Power**, chọn ***Power on*** để bật máy ảo :

    <p align=center><img src=https://i.imgur.com/57zyRqI.png width=80%></p>

- **B11 :** Sau khi đã bật máy, trong tab **Access**, chọn ***Console*** để truy cập giao diện VNC điều khiển VM :

    <p align=center><img src=https://i.imgur.com/Wm7rm8r.png width=80%></p>

    => Kết quả : Giao diện console bằng VNC :

    <p align=center><img src=https://i.imgur.com/wNtUS7M.png width=80%></p>

> <h3 align=center><strong>3 - Cài theo Template</strong></h3>
