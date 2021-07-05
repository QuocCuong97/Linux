# IPtables
## **1) Giới thiệu**
- **Iptables** là một tiện ích tường lửa cực kỳ linh hoạt được xây dựng cho các hệ điều hành Linux. Iptables giám sát lưu lượng ra vào server bằng các rule được cấu hình. Khi một kết nối cố gắng tự thiết lập trên hệ thống, **iptables** sẽ tìm một quy tắc trong danh sách để khớp với nó. Nếu không tìm thấy bất kì quy tắc nào có sẵn, thì sẽ áp dụng các rule mặc định với các gói tin.
- **Iptables** thường được cài sẵn trên các server như **Debian**, Ubuntu,.. Và mặc định không chặn bất kì port nào.
## **2) Cài đặt IPtables**
### **2.1) Cài đặt trên Debian/Ubuntu**
- **B1 :** Cài đặt `iptables` :
    ```
    $ sudo apt-get install -y iptables
    ```
- **B2 :** Kích hoạt dịch vụ `iptables` và cho phép khởi động cùng hệ thống :
    ```
    $ sudo systemctl start iptables
    $ sudo systemctl enable iptables
    ```
- **B3 :** Kiểm tra trạng thái `iptables` :
    ```
    $ sudo systemctl status iptables
    ```
- **B4 :** Hiển thị các rule đang có :
    ```
    $ iptables -nL
    ```
### **2.2) Cài đặt trên CentOS/RedHat**
- **B1 :** Trên CentOS/RedHat, mặc định sử dụng tường lửa là `firewalld`. Để cài đặt `iptables` thì trước tiên phải tắt service `firewalld`:
    ```
    # systemctl stop firewalld
    ```
- **B2 :** Không cho phép `firewalld` tự bật khi reboot server:
    ```
    # systemctl disable firewalld
    ```
- **B3 :** Đảm bảo không cho các dịch vụ khác start `firewalld`:
    ```
    # systemctl mask --now firewalld
    ```
- **B4 :** Cài đặt packages `iptables-services` :
    ```
    # yum install -y iptables-services
    ```
- **B5 :** Kích hoạt dịch vụ `iptables` và cho phép khởi động cùng hệ thống :
    ```
    # systemctl start iptables
    # systemctl enable iptables
    ```
- **B6 :** Kiểm tra trạng thái `iptables` :
    ```
    # systemctl status iptables
    ```
    <img src=https://i.imgur.com/orRHKsx.png>
- **B7 :** Hiển thị các rule đang có :
    ```
    # iptables -L -v
    ```
## **3) Cách quản lý của IPtables**
### **3.1) Các cấu hình của IPtables**
- `INPUT`: lọc gói khi đi vào trong server.
- `FORWARD`: lọc gói khi đi đến các server khác.
- `OUTPUT`: lọc gói khi ra khỏi server.
### **3.2) Các action thông dụng**
- `ACCEPT`: cho phép kết nối
- `DROP`: kết nối sẽ bị chặn và không có bất kì phản hồi nào cho server gửi đến. Thường được áp dụng cho các IP có hành động tấn công server. Cho các server đó biết là IP này không phản hồi như các IP không tồn tại.
- `REJECT`: Không cho phép kết nối, nhưng phản hồi lại lỗi. Điều này là tốt nhất nếu bạn không muốn một nguồn cụ thể kết nối với hệ thống của mình, nhưng bạn muốn họ biết rằng tường lửa của bạn đã chặn họ.
- **VD :** Khi client thực hiện `ping` :
    - Client được `ACCEPT` :
        
        <img src=https://i.imgur.com/k5KPDXk.png>

    - Client bị `DROP` :

        <img src=https://i.imgur.com/NCP0uQS.png>

    - Client bị `REJECT` :

        <img src=https://i.imgur.com/EDNekx7.png>

## **4) Mở hoặc chặn các port trên IPtables**
### **4.1) Mở port**
- Chỉ nên mở những port cần thiết và nắm rõ việc hoạt động của các port đó. Những dịch vụ thưởng được mở port `INPUT` là:
    - SSH : port `22`
    - Web: http – `80`, https – `443`
- Nếu là web server thì chỉ nên mở port SSH và Web. Ngoài ra nếu là server chạy hosting hay các dịch vụ mail thì mở các port theo từng service như: SMTPS – `465/587`, POP3 – `110`, POP3s – `995`, MAP – `143` và IMAPs – `993`…
- Sau khi liệt kê các port cần mở cho server ta dùng lệnh sau để cấu hình:
    ```
    iptables -A INPUT -p tcp -m tcp --dport xxx -j ACCEPT
    ```
    - Trong đó :
        - `-A` : append - chèn vào chuỗi `INPUT` (chèn xuống cuối), port `xxx` giao thức TCP.
- Hoặc sử dụng lệnh sau để không ảnh hưởng tới các rule có sẵn :
    ```
    iptables -I INPUT -p tcp -m tcp --dport xxx -j ACCEPT
    ```
    - Trong đó :
        - `-I` : insert - chèn vào chuỗi `INPUT`
### **4.2) Chặn kết nối**
- **VD1 :** Chặn kết nối từ server có IP là `10.11.12.13` :
    ```
    iptables -A INPUT -s 10.11.12.13 -j DROP
    ```
- **VD2 :** Chặn kêt nối đến port SSH từ IP `10.11.12.13` :
    ```
    iptables -A INPUT -p tcp --dport ssh -s 10.11.12.13 -j DROP
    ```
- **VD3 :** Chặn kết nối đến port SSH :
    ```
    iptables -A INPUT -p tcp --dport ssh -j DROP
    ```
### **4.3) Xem lại các rule đã cấu hình**
- Xem lại các rule đã cấu hình bằng lệnh sau:
    ```
    iptables –L -v
    ```
    <img src=https://i.imgur.com/Bw9Wl37.png>

    - Trong đó:
        - `target` : action sẽ thực thi cho mỗi chuỗi quy tắc
        - `prot` - protocol: các giao thức sẽ được áp dụng để thực thi các target. Có 3 lựa chọn gồm `all`, `tcp`, `udp`. Các ứng dụng như `SSH`, `FTP`, `sFTP`,... đều sử dụng `tcp`.
        - `in`: thiết bị mạng nhận kết nối vào được áp dụng cho quy tắc, chẳng hạn như `lo`, `eth0`, `eth1`.
        - `out` : thiết bị mạng phục vụ nhu cầu gửi kết nối ra ngoài được áp dụng quy tắc.
        - `destination` : địa chỉ đích được áp dụng quy tắc
- Nếu muốn xóa tất cả các cấu hình để làm lại thì ta gõ lệnh sau:
    ```
    iptables -F
    ```
### **4.4) Lưu cấu hình IPtables**
- Sau khi đã cấu hình xong các port mở chặn ta tiến hành lưu các rule đã cấu hình vào file.
    ```
    service iptables save
    service iptables restart
    ```
    - Các rule sẽ được lưu vào file: `/etc/sysconfig/iptables`
- Có thể sử dụng lệnh sau để lưu ra một file backup :
    ```
    iptables-save > /usr/src/iptables-rule
    ```
- Khôi phục lại các rule đã tạo từ file backup :
    ```
    iptables-restore < /etc/sysconfig/iptables
    iptables-restore < /usr/src/iptables-rule
    ```
> ***Chú ý :*** 
- File `/etc/sysconfig/iptables-config` có chứa một số cấu hình cho dịch vụ `iptables`. Nên tìm hiểu một số lệnh trong file này để ứng dụng.
    - `IPTABLES_SAVE_ON_STOP = “no”`:  Giá trị này nên chuyển thành `“yes”` khi đó mỗi lần stop `iptables` (hay là khi tắt máy) thì các rules trong bộ nhớ sẽ được save vào file `/etc/sysconfig/iptables` để tải lại các lần sau
    - `IPTABLES_SAVE_ON_RESTART =”no”`: Giá trị này nếu sửa thành `“yes”` thì mỗi khi restart dịch vụ `iptables`, các rules cũng được ghi lại vào file
## **5) Một số chú ý quan trọng**
- RULE đầu tiên:
    - Trước khi tạo bất cứ một rules nào bạn cần phải tạo một rule cho phép chính mình và đặt ở đầu file iptables. Bởi vì mọi firewall đều làm việc theo nguyên tắc duyệt các rule từ trên xuống dưới, nếu thỏa mãn thì áp dụng ngay bất kể các rule bên dưới có match hay không. Vậy nên các trong khi thao tác với iptables, bạn cần biết IP của máy mình đang dùng là gì. Nếu có ip tĩnh thì quá tốt, nếu không thì cũng phải biết dải ip mà ADSL của mình thường xuyên được cấp. Sau đó tạo một rules đầu tiên trong file iptables như sau:
        ```
        -A INPUT -s 113.123.133.143/32 -j ACCEPT
        ```
    - Trong đó 113.123.133.143 giả định là ip mà máy của bạn đang dùng. Nếu như bạn có một dài IP hay dùng thì thay bằng dòng
        ```
        -A INPUT -s 113.123.133.0/24 -j ACCEPT
        ```
    - Như vậy bất kể sau đó bạn có khai báo một lệnh DROP hay REJECT nào thì ip của bạn vẫn access được đến server mà không sợ thiết lập rules “tự tử” cấm chính mình.
- RULE thứ 2:
    - Cho dù bạn cẩn thận đến đâu, kể cả khi bạn đã áp dụng nguyên tắc thứ nhất thì vẫn có thể bạn không access được vào server, chẳng hạn như bạn đã mở ip của bạn, nhưng mạng của nhà cung cấp dịch vụ lại cấm ip của bạn thì sao? hoặc giả bạn cho phép dải ip ở công ty của bạn access, nhưng bạn đang ở quán cafe wifi và muốn access vào VPS thì sao? Để tránh tình huống này bạn cần tạo ra một rule thứ 2 cho phép một máy dự phòng và máy này thì bạn có thể remote từ xa vào để thao tác. Hãy add thêm dòng lệnh sau vào ngay bên dưới dòng lệnh nêu trên trong file iptables
        ```
        -A INPUT -s 14.115.112.45/32 -j ACCEPT
        ```
        trong đó 14.115.112.45 là máy dự phòng để bạn có thể access vào máy chủ khi mà IP thứ nhất bị cấm.
-----------
Tìm hiểu thêm :
- https://wiki.matbao.net/kb/bao-mat-may-chu-linux-huong-dan-cau-hinh-iptables-can-ban/