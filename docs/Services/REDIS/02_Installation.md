# Cài đặt Redis
## **1) Cài đặt trên CentOS 7**
- **B1 :** Cài đặt `epel-release` :
    ```
    # yum install -y epel-release
    ```
- **B2 :** Cài đặt `redis` :
    ```
    # yum install -y redis
    ```
- **B3 :** Kích hoạt dịch vụ và cho phép khởi động cùng hệ thống :
    ```
    # systemctl start redis
    # systemctl enable redis
    ```
- **B4 :** Kiểm tra trạng thái dịch vụ :
    ```
    # systemctl status redis
    ```
    <img src=https://i.imgur.com/oURu3nk.png>
- **B5 :** Kiểm tra phiên bản đã cài đặt :
    ```
    # redis-server --version
    ```
    <img src=https://i.imgur.com/xXUV2Ej.png>