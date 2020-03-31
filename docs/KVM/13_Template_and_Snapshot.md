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
- Có 2 loại snapshot chính được hỗ trợ bởi libvirt:
    - ***Internal*** : Trước và sau khi tạo snapshot, dữ liệu chỉ được lưu trên một ổ đĩa duy nhất. Người dùng có thể tạo internal snapshot bằng công cụ virt-manager. Mặc dù vậy, nó vẫn có 1 vài hạn chế :
        - Chỉ hỗ trợ duy nhất định dạng `.qcow2`
        - VM sẽ bị ngưng lại khi tiến hành snapshot
        - Không hoạt động với LVM storage pools
    - ***External*** : Dựa theo cơ chế copy-on-write. Khi snapshot được tạo, ổ đĩa ban đầu sẽ có trạng thái "read-only" và có một ổ đĩa khác chồng lên để lưu dữ liệu mới:

        <p align=center><img src=https://i.imgur.com/kTfSDgR.png></p>

        - Ổ đĩa được chồng lên được tạo ra có định dạng `.qcow2` , hoàn toàn trống và nó có thể chứa lượng dữ diệu giống như ổ đĩa ban đầu. External snapshot có thể được tạo với bất kì định dạng ổ đĩa nào mà libvirt hỗ trợ. Tuy nhiên không có công cụ đồ họa nào hỗ trợ cho việc này.
- Một vài câu lệnh virsh liên quan tới việc tạo và quản lí máy ảo:
    - `snapshot-create` : Tạo snapshot từ file XML
    - `snapshot-create-as` : Tạo snapshot với những tùy chọn
    - `snapshot-current` : Thiết lập hoặc lấy thông tin của snapshot hiện tại
    - `snapshot-delete` : Xóa một snapshot
    - `snapshot-dumpxml` : Tạo ra thêm 1 file XML cho một snapshot
    - `snapshot-edit` : Chỉnh sửa file XML của snapshot
    - `snapshot-info` : Lấy thông tin của snapshot
    - `snapshot-list` : Lấy danh sách các snapshots
    - `snapshot-parent` : Lấy tên của snapshot "cha" của một snapshot nào đó
    - `snapshot-revert` : Quay trở về trạng thái khi tạo snapshot
### **2.1) Tạo và quản lí Internal Snapshot**
- Tạo mới snapshot :
    ```
    # virsh snapshot-create-as --domain CentOS7-01 --name "Begin" --description "ban khoi tao" --atomic
    ```
    > option `--atomic` đảm bảo cho sự vẹn toàn dữ liệu

    <img src=https://i.imgur.com/bTPCsIY.png>

- Kiểm tra snapshot vừa tạo :
    ```
    # virsh snapshot-list CentOS7-01
    ```
    <img src=https://i.imgur.com/QnRYwWZ.png>

- Kiểm tra thông tin chi tiết bản snapshot "`Begin`" :
    ```
    # virsh snapshot-info CentOS7-01 Begin
    ```
    <img src=https://i.imgur.com/nMynlp9.png>
    
    - Bản snapshot được tạo ra riêng biệt không liên quan đến máy ảo . Máy ảo vẫn sử dụng disk của nó. Kiểm tra bằng lệnh "`virsh domblklist CentOS7-01`"

        <img src=https://i.imgur.com/RYj4Kow.png>

- Reverse lại bản snapshot "`Begin`" :
    ```
    # virsh snapshot-revert CentOS7-01 --snapshotname "`Begin`"
    ```
    - Thử tạo thêm 1 bản snapshot mới từ bản snapshot này :
        ```
        # virsh snapshot-create-as --domain CentOS7-01 --name "Begin-Child" --atomic
        ```
    - Kiểm tra lại bằng lệnh `snapshot-list --parent` ta sẽ thấy được mối quan hệ của 2 bản snapshot trên :
        ```
        # virsh snapshot-list --parent CentOS7-01
        ```
        <img src=https://i.imgur.com/EzawIy1.png>
- Xóa các bản snapshot vừa tạo :
    ```
    # virsh snapshot-delete CentOS7-01 --snapshotname "`Begin`"
    ```
    <img src=https://i.imgur.com/FZl4vFU.png>
    
    > Thao tác này không xóa đi snapshot con của nó

### **2.2) Tạo và quản lí External Snapshot**
- **B1 :** Thực hiện kiểm tra ổ đĩa của máy ảo muốn tạo snapshot :
    ```
    # virsh domblklist CentOS7-01
    ```
    <img src=https://i.imgur.com/zJGFHEY.png>

- **B2 :** Tiến hành tạo snapshot bằng câu lệnh :
    ```
    # virsh snapshot-create-as CentOS7-01 --name "Begin" --description "ban khoi tao --disk-only --atomic
    ```
    > Trong đó `--disk-only` dùng để tạo snapshot cho riêng ổ đĩa .
