# Tổng quan về Apache
## **1) Web Server là gì**
- **Web Server** là 1 dịch vụ mạng hướng nội dung của người dùng lên giao diện Web .
- Nhiệm vụ của **Web Server** là đưa website lên Internet . Để làm được điều đó , nó hoạt động giống như là một người đứng giữa Server và máy khách Client . Nó sẽ kéo nội dung từ Server về cho mỗi một truy vấn xuất phát từ máy khách để hiển thị kết quả tương ứng dưới hình thức là một Website .
- Điểm khó khăn lớn nhất của một **Web Server** là kéo dữ liệu cho nhiều người dùng cùng một lúc – vì mỗi một người lại cũng đang truy vấn tới các trang web khác nhau . **Web server** xử lý các file này dưới ngôn ngữ lập trình như là `PHP` , `Python` , `Java` ,... Những ngôn ngữ này biến chúng thành file `HTML` và file trên trình duyệt cho người dùng web thấy được .
- **Web Server** còn được gọi là **HTTP server** và chúng sử dụng giao thức **HTTP - hypertext transport protocol** ( port `80/TCP` )
- Các **Web Server** có thể cài lên CentOS 7 là :
    - **Apache HTTP Server**
    - **Apache Tomcat**
    - **nginx**
    - **OpenLiteSpeed**
## **2) Apache HTTP Server** <img src=https://i.imgur.com/aeHvD7d.png align=right width=35%>
- Trang chủ : http://httpd.apache.org/
- Source Code : https://github.com/apache/httpd
- **Apache** là phần mềm web server mã nguồn mở đa nền tảng miễn phí , sử dụng giấy phép **Apache License `2.0`**
- Ra đời năm `1995` bởi **Robert McCool**
- Phiên bản ổn định mới nhất : `2.4.39` ( `tháng 4-2019` ) , tuy nhiên đã phát hành tới bản `2.4.9`
- **Apache** được phát triển và duy trì bởi một cộng đồng các nhà phát triển dưới sự bảo trợ của **Quỹ phần mềm Apache ( Apache Software Foundation )** . 
- Phần lớn các phiên bản **Apache** chạy trên bản phân phối Linux , nhưng các phiên bản hiện tại cũng chạy trên Microsoft Windows và nhiều hệ thống tương tự Unix . Các phiên bản trước đây cũng chạy trên OpenVMS , NetWare và các hệ điều hành khác .
- Là 1 thành phần của **LAMP Stack** - ( `Linux` , `Apache` , `MariaDB` , `Perl/Python/PHP` )
- Theo [**NetCraft**](https://news.netcraft.com/archives/2019/07/26/july-2019-web-server-survey.html) , tính đến tháng `7-2019` , **Apache** đang chiếm đến khoảng `27.75%` thị phần websites trên toàn thế giới .

    <img src=https://i.imgur.com/0aAfvb2.png>

- **Ưu điểm**
    - Phần mềm mã nguồn mở và miễn phí, kể cả cho mục đích thương mại
    - Phần mềm đáng tin cậy, ổn định
    - Được cập nhật thường xuyên, nhiều bản vá lỗi bảo mật liên tục
    - Linh hoạt vì có cấu trúc module
    - Dễ cấu hình, thân thiện với người mới bắt đầu
    - Đa nền tảng (hoạt động được cả với server Unix và Windows )
    - Hoạt động cực kỳ hiệu quả với **WordPress** sites
    - Có cộng đồng lớn và sẵn sàng hỗ trợ với bất kỳ vấn đề nào
- **Nhược điểm** :
    - Gặp vấn đề hiệu năng nếu website có lượng truy cập cực lớn
    - Quá nhiều lựa chọn thiết lập có thể gây ra các điểm yếu bảo mật
## **3) Các lệnh cơ bản về Apache**
### **3.1) Cài đặt Apache**
- **B1 :** Cài đặt repo `Epel` :
    ```
    # yum install -y epel-release
    ```
- **B2 :** Cài đặt gói `httpd` :
    ```
    # yum install -y httpd
    ```
    > Mặc định khi cài qua `yum` , trên CentOS `6` sẽ cài Apache `2.2` , còn trên CentOS `7` sẽ cài `2.4` .
### **3.2) Gỡ cài đặt Apache**
```
# yum remove httpd -y
```
### **3.3) Kiểm tra version Apache đã cài**
```
# httpd -v
```
<img src=https://i.imgur.com/U5g2ZWo.png>

## **4) Cài đặt Apache từ Source**
- **B1 :** Download source **`httpd`** về từ Internet và lưu vào thư mục `/var/tmp`
    ```
    # cd /var/tmp
    # wget https://archive.apache.org/dist/httpd/httpd-2.4.35.tar.gz
    # tar -zxvf httpd-2.4.35.tar.gz
    ```
    > Có thể thay `2.4.35` bằng số khác để tải về các phiên bản khác
- **B2 :** Cài đặt các gói thư viện bổ sung
    - **APR `1.7.0`**
        ```
        # wget https://www.apache.org/dist/apr/apr-1.7.0.tar.gz
        # cd apr-1.7.0/
        # ./configure
        # make && make install
        ```
    - **Expat-devel `2.1.0`**
        ```
        # wget http://mirror.centos.org/centos/7/os/x86_64/Packages/expat-devel-2.1.0-10.el7_3.x86_64.rpm
        # rpm -ivh expat-devel-2.1.0-10.el7_3.x86_64.rpm
        ```
    - **APR-util `1.6.1`**
        ```
        # wget https://www.apache.org/dist/apr/apr-util-1.6.1.tar.gz
        # cd apr-util-1.6.1/
        # ./configure --with-apr=/usr/local/apr/bin/apr-1-config
        # make && make install
        ```
    - **PCRE `8.4.3`** ( **`8.21`** trở xuống cho **Apache `2.2`**)
        ```
        # wget -O pcre-8.43.tar.gz https://sourceforge.net/projects/pcre/files/pcre/8.43/pcre-8.43.tar.gz/download
        # cd pcre-8.43/
        # ./configure
        # make && make install
        ```
- **B3 :** Biên dịch file `httpd` và cài đặt :
    ```
    # cd httpd-2.4.35/
    # ./configure
    # make && make install
    ```
- **B4 :** Khởi động dịch vụ `httpd` :
    ```
    # systemctl start httpd
    ```
## **5) Các file/thư mục quan trọng của Apache**
- `/var/html/` : là thư mục gốc chứa các file `htm` , `html` , `images` .... tạo thành nội dung cho trang web
- `/etc/httpd/` : thư mục chứa tất cả các file cấu hình cho **Apache** :
    - `/etc/httpd/conf/httpd.conf` : file cấu hình chính của dịch vụ Apache
    - `/etc/httpd/conf.d/` : thư mục chứa các cấu hình bổ sung cho Apache
        - `/etc/httpd/conf.d/vhost.conf` : file cấu hình virtual host
    - `/etc/httpd/conf.modules.d/` : thư mục chứa file cấu hình của các modules
- `/var/log/httpd/error_log` : file chứa log lỗi dịch vụ
- `/var/log/httpd/access_log` : file chứa log đăng nhập
--------
**THAM KHẢO**
- https://www.hostinger.vn/huong-dan/apache-la-gi-giai-thich-cho-nguoi-moi-bat-dau-hieu-ve-apache-web-server/
- https://en.wikipedia.org/wiki/Apache_HTTP_Server
- https://cuongquach.com/compile-apache-22-24-tren-centos-6-7.html



## https://www.linode.com/docs/web-servers/apache-tips-and-tricks/apache-configuration-basics/