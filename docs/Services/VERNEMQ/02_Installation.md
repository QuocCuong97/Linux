# Cài đặt VerneMQ
## **1) Cài đặt trên CentOS 7**
- **B1 :** Download package `.rpm` :
    ```
    # wget https://github.com/vernemq/vernemq/releases/download/1.11.0/vernemq-1.11.0.centos7.x86_64.rpm
    ```
- **B2 :** Cài đặt package :
    ```
    # rpm -Uvh vernemq-1.11.0.centos7.x86_64.rpm
    ```
- **B3 :** Chấp nhận điều khoản dịch vụ trong file `/etc/vernemq/vernemq.conf` :
    ```
    # vi /etc/vernemq/vernemq.conf
    ```
    - Chỉnh sửa như sau :
        ```
        accept_eula = yes
        ```
- **B3 :** Kích hoạt dịch vụ :
    ```
    # systemctl start vernemq
    ```
- **B4 :** Kiểm tra lại trạng thái dịch vụ :
    ```
    # systemctl status vernmq
    ```
    <img src=https://i.imgur.com/xeFRUk2.png>
- **B5 :** Kiểm tra phiên bản **VerneMQ** :
    ```
    # vernemq version
    ```
    <img src=https://i.imgur.com/jlBTr71.png>