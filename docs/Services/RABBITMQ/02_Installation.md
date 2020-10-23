# Cài đặt RabbitMQ
## **1) Cài đặt trên CentOS 7**
- **B1 :** Cài đặt `epel-release` và update các gói sẵn có :
    ```
    # yum install -y epel-release
    # yum update -y
    ```
- **B2 :** **RabbitMQ** được viết bằng ngôn ngữ **Erlang**, vì vậy cần cài đặt **Erlang** :
    ```
    # yum -y install erlang socat
    ```
    - Kiểm tra lại phiên bản `erlang` :
        ```
        # erl -version
        ```
        <img src=https://i.imgur.com/DXWSgOM.png>
- **B3 :** Download **RabbitMQ** :
    ```
    # wget https://www.rabbitmq.com/releases/rabbitmq-server/current/rabbitmq-server-3.6.15-1.el7.noarch.rpm
    ```
- **B4 :** Import GPG Key :
    ```
    # rpm --import https://www.rabbitmq.com/rabbitmq-release-signing-key.asc
    ```
- **B5 :** Cài đặt package vừa tải về :
    ```
    # rpm -Uvh rabbitmq-server-3.6.15-1.el7.noarch.rpm
    ```
- **B6 :** Cho phép dịch vụ qua **firewalld** :
    ```
    # firewall-cmd --zone=public --permanent --add-port=4369/tcp --add-port=25672/tcp --add-port=5671-5672/tcp --add-port=15672/tcp  --add-port=61613-61614/tcp --add-port=1883/tcp --add-port=8883/tcp
    # firewall-cmd --reload
    ```
- **B7 :** Kích hoạt dịch vụ và cho phép khởi động cùng hệ thống :
    ```
    # systemctl start rabbitmq-server
    # systemctl enable rabbitmq-server
    ```
- **B8 :** Kiểm tra trạng thái dịch vụ :
    ```
    # systemctl status rabbitmq-server
    ```
    <img src=https://i.imgur.com/dq6iI3R.png>
- **B9 :** Kích hoạt **RabbitMQ management console** để có thể giám sát các tiến trình trong **RabbitMQ server** thông qua giao diện web :
    ```
    # rabbitmq-plugins enable rabbitmq_management
    # chown -R rabbitmq:rabbitmq /var/lib/rabbitmq/
    ```
- **B10 :** Tạo tài khoản và gắn tag phân quyền để truy cập **RabbitMQ management console** :
    ```
    # rabbitmqctl add_user mqadmin Password123
    # rabbitmqctl set_user_tags mqadmin administrator
    # rabbitmqctl set_permissions -p / mqadmin ".*" ".*" ".*"
    ```
- **B11 :** Truy cập giao diện **RabbitMQ management console** từ trình duyệt, nhập user vừa tạo :
    ```
    http://[rabbitmq-server-IP]:15672/
    ```
    <img src=https://i.imgur.com/CWCwYBt.png>

    - Giao diện trang quản lý :

        <img src=https://i.imgur.com/gMNOSQr.png>