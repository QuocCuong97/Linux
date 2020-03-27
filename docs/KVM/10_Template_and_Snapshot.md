# Template và Snapshot
## **1) Template trong KVM**
### **1.1) Giới thiệu**
- **Template** là một dạng file image pre-configured của hệ điều hành được dùng để tạo nhanh các máy ảo . 
- Sử dụng **template** sẽ khiến bạn tránh khỏi những bước cài đặt lặp đi lặp lại và tiết kiệm thời gian rất nhiều so với việc cài bằng tay từng bước một .
### **1.2) Tạo và quản lý template**
- Hai khái niệm mà người dùng cần phân biệt đó là **clone** và **template** . Nếu **clone** đơn thuần chỉ là một bản sao của máy ảo thì **template** được coi là ***master copy*** của VM , nó có thể được dùng để tạo ra rất nhiều clone khác nữa .
- Có hai phương thức để triển khai máy ảo từ **template** đó là **Thin** và **Clone** :
    - **Thin** : Máy ảo được tạo ra theo phương thức này sẽ sử dụng **template** như một base image, lúc này nó sẽ được chuyển sang trạng thái read only. Cùng với đó, sẽ có một ổ mới hỗ trợ "copy on write" được thêm vào để lưu dữ liệu mới. Phương thức này tốn ít dung lượng hơn tuy nhiên các VM được tạo ra sẽ phụ thuộc vào base image, chúng sẽ không chạy được nếu không có base image .
    - **Clone** : Máy ảo được tạo ra làn một bản sao hoàn chỉnh và hoàn toàn không phụ thộc vào template cũng như máy ảo ban đầu. Mặc dù vậy, nó sẽ chiếm dung lượng giống như máy ảo ban đầu.
- Các bước tạo **template** :
    - **B1 :** Cài đặt máy ảo với đầy đủ các phần mềm cần thiết để biến nó thành template .
    - **B2 :** Loại bỏ tất cả những cài đặt cụ thể ví dụ như password SSH, địa chỉ MAC... để đảm bảo rằng nó sẽ không được áp dụng giống nhau tới tất cả các máy ảo được tạo ra sau này.
    - **B3 :** Đánh dấu máy ảo là template bằng việc đổi tên.
- Sử dụng `virt-sysprep` để "niêm phong" máy ảo:
    - `virt-sysprep` là một tiện ích nằm trong gói `libguestfs-tools-c` được sử dụng để loại bỏ những thông tin cụ thể của hệ thống đồng thời niêm phong và biến máy ảo trở thành **template**
    - Có 2 options để dùng `virt-sysprep` : 
        - `-d` : sử dụng với tên hoặc UUID của máy ảo
        - `-a` : được sử dụng với đường dẫn tới ổ đĩa máy ảo.
### **1.3) Lab tạo Template**
#### **Tạo template**
- **B1 :** Tạo 1 máy ảo tên là `Centos7-test` trên host KVM . Cài đặt các gói cần thiết cho máy ảo để làm template
- **B2 :** Shutdown máy ảo :
    ```
    # virsh shutdown Centos7-test
    ```
- **B3 :** Cài đặt gói `libguestfs-tools-c` :
    ```
    # yum -y install libguestfs-tools-c
    ```
- **B4 :** Sử dụng `virt-sysprep` để loại bỏ các thông tin cấu hình như UUID, MAC, ... đồng thời niêm phong và biến máy ảo thành template :
    ```
    # virt-sysprep -d Centos7-test
    ```
    <img src=https://i.imgur.com/O6sGyPo.png>

- **B5 :** Backup file `.xml` của template :
    ```
    # virsh dumpxml Centos7-test > /root/template.xml
    ```
- **B6 :** Máy ảo `Centos7-test` đã không còn toàn vẹn. Vì vậy sẽ tiến hành undefine nó :
    ```
    # virsh undefine Centos7-test
    ```
    <img src=https://i.imgur.com/i0P1fta.png>

#### **Sử dụng template**
- **B6 :** Tạo 1 disk cho máy ảo mới và backup bằng image của máy ảo cũ :
    ```
    # qemu-img create -b /var/lib/libvirt/images/centos7-test -f qcow2 /var/lib/libvirt/images/vm1.qcow2
    ```
- **B7 :** Kiểm tra xem file mới tạo ra đã được chỉ tới file backup của nó hay chưa bằng câu lệnh : 
    ```
    # qemu-img info /var/lib/libvirt/images/vm1.qcow2
    ```
    <img src=https://i.imgur.com/KMcXj1f.png>

- **B8 :** Dùng `virt-clone` để tạo ra máy ảo mới từ file `.xml` :
    ```
    # virt-clone --original-xml /root/template.xml -f /var/lib/libvirt/images/vm1.qcow2 -n vm1 --preserve-data
    ```
    <img src=https://i.imgur.com/fnTwjlQ.png>
    
    - Kiểm tra dung lượng các disk sau khi clone :

        <img src=https://i.imgur.com/u3gG8Lg.png>

    - Thử download file trên `vm1` và kiểm tra lại :
        ```
        # yum install -y wget
        # wget https://raw.githubusercontent.com/QuocCuong97/Code/master/Python/lamp/lamp_one_file.py
        ```
        <img src=https://i.imgur.com/PP3qrp5.png>
    
    => Nhận xét : máy ảo `vm1` tạo ra sử dụng hoàn toàn OS trên disk image cũ . Disk image của nó chỉ tăng dung lượng khi download hoặc cài đặt thêm file và ứng dụng . => Nếu xóa file disk image cũ thì máy ảo mới tạo cũng sẽ không dùng được nữa

    > Để tạo ra một máy ảo mới không phụ thuộc vào disk image ban đầu, sử dụng option `--auto-clone` thay vì `--preverse-data` :

    ```
    # virt-clone --original-xml /root/template.xml -f /var/lib/libvirt/images/vm1.qcow2 -n vm2 --auto-clone
    ```
## **2) Snapshot trong KVM**
- **Snapshot** là trạng thái của hệ thống ở một thời điểm nhất định, nó sẽ lưu lại cả những cài đặt và dữ liệu. Với **snapshot**, có thể quay trở lại trạng thái của máy ảo ở một thời điểm nào đó rất dễ dàng .
- `libvirt` hỗ trợ việc tạo **snapshot** khi máy ảo đang chạy. Mặc dù vậy, nếu máy ảo của bạn đang chạy ứng dụng thì tốt hơn hết hãy tắt hoặc suspend trước khi tiến hành tạo **snapshot** .


