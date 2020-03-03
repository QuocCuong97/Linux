# Tổng quan về Nginx
## **1) Giới thiệu** <img src=https://i.imgur.com/IiTWNL2.png align=right width=40%>
- **NGINX** được phát âm là "`engine-ex`" , là một dịch vụ web server mã nguồn mở .
- **NGINX** khởi đầu là một dịch vụ web server, nhưng bây giờ cũng được sử dụng phổ biến như một máy chủ proxy ( **reverse proxy server** ), **HTTP cache** hoặc dùng làm cân bằng tải ( load balancer ) .
- **NGINX** được phát triển bởi **Igor Sysoev** vào năm `2002` , với phiên bản phát hành công khai đầu tiên vào tháng `10/2004` . **Igor** xem phần mềm này ban đầu như một câu trả lời cho vấn đề **C10k** ( vấn đề liên quan đến hiệu suất xử lý `10.000` kết nối cùng lúc ) .
- Với mục tiêu của **NGINX** là tối ưu hóa hiệu suất , nó thường vượt mặt các web server khác trong các bài kiểm tra benchmark . Đặc biệt trong các trường hợp cần phục vụ nội dung file tĩnh ( css, js, img,.... ) hoặc các yêu cầu truy vấn đồng thời số lượng lớn ( *high concurrent request* ) .
- Trang chủ : https://nginx.org
- Phiên bản ổn định mới nhất : `1.16.1`
## **2) Một số chức năng của NGINX**
- **NGINX** được phát triển cho các mục đích tối ưu việc sử dụng RAM (bộ nhớ thấp) nhưng phục vụ được nhiều kết nối đồng thời cao hơn . **NGINX** sử dụng kiến trúc hướng sự kiện ( **event-driven** ) không đồng bộ ( **asynchronous** ) và có khả năng mở rộng . Ngay cả khi không cần xử lý hàng nghìn truy vấn đồng thời , vẫn nên sử dụng **NGINX** do hiệu xuất cao và yêu cầu bộ nhớ thấp của **NGINX** so với **Apache** .
- Một vài tính năng thông thường của **NGINX** bao gồm :
    - Reverse Proxy với caching
    - IPv6
    - Cân bằng tải
    - FastCGI hỗ trợ với caching
    - Redirect URL
    - Xử lý file tĩnh, file `index` và auto-indexing
    - **TLS/SSL** với SNI
    - Hỗ trợ nhúng Perl, Lua,...
    - Hỗ trợ WebSockets
    - Giới hạn kết nối đồng thời từ địa chỉ IP
    - Rewrite URL
## **3) So sánh NGINX và Apache**
## **4) Cài đặt NGINX**
### **4.1) Cài đặt trên CentOS 7**
- **B1 :** Update các package có sẵn :
    ```
    # yum update -y
    # yum upgrade -y
    ```
- **B2 :** Khởi tạo repo **NGINX** :
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
- **B3 :** Import chữ ký xác thực :
    ```
    # wget --no-check-certificate -O nginx_signing.key https://nginx.org/keys/nginx_signing.key
    # rpm --import nginx_signing.key
    ```
- **B4 :** Cập nhật thông tin repo :
    ```
    # yum repolist
    ```
- **B5 :** Cài đặt **NGINX** thông qua repo trước đó :
    ```
    # yum --disablerepo=* --enablerepo=nginx install nginx -y
    ```
- **B6 :** Cấu hình **firewalld** cho phép port 80 :
    ```
    # firewall-cmd --zone=public --permanent --add-port=80/tcp
    # firewall-cmd --reload
    ```
- **B7 :** Khởi động và cấu hình startup cho dịch vụ :
    ```
    # systemctl start nginx.service
    # systemctl enable nginx.service
    ```
- **B8 :** Mở trình duyệt nhập IP của server để kiểm tra :
    ```
    http://192.168.5.10
    ```
    <img src=https://i.imgur.com/4Woea3T.png>

### **4.2) Cài đặt trên Ubuntu 18.04**
- **B1 :** Update các package sẵn có :
    ```
    $ sudo apt-get update -y
    $ sudo apt-get upgrade -y
    ```
- **B2 :** Khởi tạo repo **NGINX** trong file `sources.list` :
    ```
    $ sudo vi /etc/apt/sources.list
    ```
    - Thêm vào nội dung sau :
    ```
    deb http://nginx.org/packages/ubuntu/ bionic nginx
    deb-src http://nginx.org/packages/ubuntu/ bionic nginx
    ```
- **B3 :** Import chữ ký xác thực :
    ```
    $ wget --no-check-certificate -O nginx_signing.key https://nginx.org/keys/nginx_signing.key
    $ sudo apt-key add nginx_signing.key
    ```
- **B4 :** Cập nhật thông tin repo :
    ```
    $ sudo apt-get update
    ```
- **B5 :** Cài đặt **NGINX** :
    ```
    $ sudo apt-get install -y nginx
    ```
- **B6 :** Cấu hình firewall cho phép port `80` :
    ```
    $ sudo ufw allow 80/tcp
    ```
- **B7 :** Khởi động và cấu hình startup cho dịch vụ :
    ```
    $ service nginx start
    $ sudo update-rc.d nginx defaults
    $ sudo update-rc.d nginx enable
    ```
- **B8 :** Mở trình duyệt nhập IP của server để kiểm tra :
    ```
    http://192.168.5.188
    ```
### **4.3) Cài đặt từ source**
## **5) Các file cấu hình cơ bản của NGINX**
- `/etc/nginx/nginx.conf` : file cấu hình chính
- `/usr/local/nginx/conf.d/` : thư mục chứa các file cấu hình riêng (như virtual host,...)
- `/usr/share/nginx/html` : thư mục chứa source code web
## **6) Một số lệnh cơ bản với NGINX**
- Khởi động **NGINX** :
    ```
    # nginx
    ```
- Dừng dịch vụ **NGINX**
    ```
    # nginx -s quit
    ```
- Kiểm tra cú pháp cấu hình **NGINX** :
    ```
    # nginx -t
    ```
- Load lại cấu hình **NGINX** mà không cần khởi động lại dịch vụ :
    ```
    # nginx -s reload
    ```
- Mở trực tiếp file log của **NGINX** :"
    ```
    # nginx -s reopen
    ```
