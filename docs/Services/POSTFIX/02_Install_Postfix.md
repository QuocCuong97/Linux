# Cài đặt Postfix
## **1) Cài đặt trên CentOS 7**
- **B1 :** Kiểm tra phần mềm `sendmail` đã được cài đặt chưa :
    ```
    # rpm -qa | grep sendmail
    ```
    - Nếu đã cài đặt thì remove nó :
        ```
        # yum remove sendmail*
        ```
- **B2 :** Cài đặt `postfix` và một số gói liên quan :  
    ```
    # yum -y install postfix cyrus-sasl-plain mailx
    ```
- **B3 :** Đặt `postfix` như **MTA** mặc định của hệ thống :
    ```
    # alternatives --set mta /usr/sbin/postfix
    ```
    - Nếu câu lệnh bị lỗi và trả về output "`/usr/sbin/postfix has not been configured as an alternative for mta`" thì thực hiện lệnh sau :
        ```
        # alternatives --set mta /usr/sbin/sendmail.postfix
        ```
- **B4 :** Khởi động dịch vụ `postfix` và cho phép nó khởi động cùng hệ thống :
    ```
    # systemctl start postfix
    # systemctl enable postfix
    ```
- **B5 :** Chỉnh sửa file `main.cf` :
    ```
    # vi /etc/postfix/main.cf
    ```
    - Thêm vào cuối file :
        ```
        myhostname = hostname.example.com
        relayhost = [smtp.gmail.com]:587
        smtp_use_tls = yes
        smtp_sasl_auth_enable = yes
        smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
        smtp_tls_CAfile = /etc/ssl/certs/ca-bundle.crt
        smtp_sasl_security_options = noanonymous
        smtp_sasl_tls_security_options = noanonymous
        ```
- **B6 :** Tạo mới file `/etc/postfix/sasl_passwd` :
    ```
    # vi /etc/postfix/sasl_passwd
    ```
    - Thêm vào nội dung sau :
        ```
        [smtp.gmail.com]:587 username:password
        ```
        - `username` : tên user gmail dùng để gửi
        - `password` : password của user gmail
- **B7 :** Phân quyền cho file `sasl_password` vừa tạo :
    ```
    # postmap /etc/postfix/sasl_passwd
    # chown root:postfix /etc/postfix/sasl_passwd*
    # chmod 640 /etc/postfix/sasl_passwd*
    # systemctl reload postfix
    ```
- **B8 :** Đăng nhập bằng gmail để thực hiện gửi mail đã khai báo bên trên trên trình duyệt và truy cập vào địa chỉ sau :
    ```
    https://myaccount.google.com/lesssecureapps
    ```
    - Bật chế độ cho phép ứng dụng truy cập :

        <p align=center><img src=https://i.imgur.com/HNodki4.png width=70%></p>

- **B9 :** (Tùy chọn) Trong ngữ cảnh muốn sử dụng gmail vừa nhập cho user `root` (hoặc bất cứ user nào khác trong máy), thực hiện chỉnh sửa file `/etc/aliases` :
    ```
    # vi /etc/aliases
    ```
    - Chỉnh sửa tại dòng `96` :

        <img src=https://i.imgur.com/X2ly1zi.png>
    - Áp dụng thay đổi trên :
        ```
        # newaliases
        ```
- **B10 :** Thực hiện gửi mail :
    - **C1 :** Sử dụng lệnh `echo` :
        ```
        # echo "Send successfully" | mail -s "Mail test" root (nếu đã thực hiện B9)
        ```
        hoặc
        ```
        # echo "Send successfully" | mail -s "Mail test" <email_nhận> (nếu không thực hiện B9)
        ```
    - Kết quả : Tài khoản `<email_nhận>` đã nhận được mail :
        
        <img src=https://i.imgur.com/J5cG8M5.png>
[Tham khảo](https://www.binarytides.com/linux-mail-command-examples/)

        