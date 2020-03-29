# File image trong KVM
## **1) Tổng quan về file image trong KVM**
- Images là loại file đóng gói hết tất cả nội dung của 1 đĩa CD/DVD bên trong .
- Trong KVM Guest có 2 thành phần chính:
    - **VM definition** được lưu dưới dạng file `.xml` tại thư mục `/etc/libvirt/qemu` . File này chứa các thông tin của máy ảo như tên, thông tin về tài nguyên (CPU, RAM,...)
    - File còn loại là file disk được lưu dưới dạng image tại thư mục `/var/lib/libvirt/images` .
- 3 định dạng thông dụng nhất của file image sử dụng trong KVM là `.iso`, `.raw`, `.qcow2`
## **2) Các định dạng image phổ biến**
### **2.1) `.iso`**
- File `.iso` chứa toàn bộ dữ liệu của 1 đĩa CD/DVD
- File `.iso` thường được sử dụng để cài đặt hệ điều hành sử dụng để cài đặt hệ điều hành của VM. Người dùng có thể import trực tiếp hoặc download từ Internet .
- Boot từ file `.iso` cũng là một trong số những tùy chọn mà người dùng có thể sử dụng khi tạo máy ảo .
### **2.2) `.raw`**
- Là một định dạng image phi cấu trúc .
- Khi người dùng tạo mới một máy ảo có định dạng là `.raw` thì dung lượng của file disk sẽ đúng bằng dung lượng của ổ đĩa máy ảo vừa tạo .
- Định dạng `.raw` là hình ảnh theo dạng nhị phân (bit by bit) của ổ đĩa .
- Mặc định khi tạo máy ảo với `virt-manager` hoặc không khai báo khi tạo VM bằng `virt-install` thì định dạng ổ đĩa mặc định sẽ là `.raw` . Nói cách khác, `.raw` chính là định dạng mặc định của **QEMU** .
### **2.3) `.qcow2`**
- `.qcow` là một định dạng file image được sử dụng bởi **QEMU** . Nó viết tắt bởi "`QEMU-copy-on-write`" và sử dụng kỹ thuật tối ưu hóa lưu trữ đĩa để trì hoãn phân bổ lưu trữ đến khi nó thực sự cần thiết.
- Hai phiên bản đang tồn tại là `.qcow` và `.qcow2` 
- `.qcow2` là phiên bản cập nhật của `.qcow`, nhằm thay thế nó. Khác biệt đó là `.qcow2` hỗ trợ nhiều snapshots thông qua một mô hình mới, linh hoạt hơn
- `.qcow2` hỗ trợ copy-on-write với những tính năng đặc biệt như snapshot, mã hóa, nén dữ liệu, ..
- `.qcow2` hỗ trợ việc tăng bộ nhớ bằng cơ chế **Thin Provisioning** ( máy ảo dùng bao nhiêu file có dung lượng bấy nhiêu ) .
## **3) Cơ chế Thin Provisioning và Thick Provisioning**
### **3.1) Thinly-Provisioned Volumes ( Thin Volumes )**
- **Thin Volume** được tạo ra có 1 dung lượng chia sẵn ( ***allocated size*** ) nhưng chỉ chiếm dung lượng của ổ đĩa đúng bằng dung lượng của dữ liệu thực tế có trên **Volume**  ( ***used size*** ) .
- **VD :** chia ổ ảo `30G` nhưng hiện tại chỉ sử dụng `10G` thì trên ổ đĩa vật lý chỉ chiếm `10G` không gian thực .

    <p align=center><img src=https://i.imgur.com/cYzr8td.jpg width=80%></p>

### **3.2) Thickly-Provisioned Volumes ( Thick Volumes )**
- **Thick Volume** được tạo ra có 1 dung lượng chia sẵn ( ***allocated size*** ) và chiếm đúng bằng đó dung lượng của ổ đĩa mặc dù dữ liệu bên trong ít hơn .
- **VD :** chia ổ ảo `30G` , thực tế đang sử dụng hết `10G` nhưng trên ổ đĩa vật lý vẫn chiếm `30G` không gian đĩa .

    <p align=center><img src=https://i.imgur.com/B4XiRaQ.jpg width=80%></p>

## **4) Chuyển đổi định dạng giữa `.raw` và `.qcow2`**
- Để chuyển từ `.qcow2` sang `.raw` sử dụng lệnh :
    ```
    # qemu-img convert -f qcow2 -O raw [old_file_qcow2] [new_file_raw]
    ```
- Để chuyển từ `.raw` sang `.qcow2` sử dụng lệnh :
    ```
    # qemu-img convert -f raw -O qcow2 [old_file_raw] [new_file_qcow2]
    ```
- Sau khi chuyển đổi định dạng file, cần chỉnh sửa lại file cấu hình của VM bằng lệnh :
    ```
    # virsh edit [VM_name]
    ```
- **VD :** Chuyển đổi định dạng file `centos7-02.qcow2` sang `centos7-02.raw` :
    - Thực hiện lệnh :
        ```
        # qemu-img convert -f qcow2 -O raw /var/lib/libvirt/images/centos7-02.qcow2 /var/lib/libvirt/images/centos7-02.raw
        ```
    - Sau khi thực hiện lệnh chuyển đổi, file `.qcow2` vẫn tồn tại, chỉ mất khi bị xóa đi . File `.raw` cũng đã chiếm toàn bộ dung lượng disk `10G` được cấu hình từ đầu, khác với file `.qcow2` chỉ chiếm phần dung lượng sử dụng :
        ```
        # ls -lh /var/lib/libvirt/images
        -rw-r--r--. 1 root root 193K Mar 25 16:09 centos7-02.qcow2
        -rw-r--r--. 1 root root  10G Mar 25 22:52 centos7-02.raw
        ```
    - Thực hiện sửa file cấu hình của VM để nhận disk mới :
        ```
        # virsh edit CentOS7-02
        ```
        - Cấu hình disk ban đầu :

            <img src=https://i.imgur.com/qHXonjg.png>
        
        - Cấu hình disk sau khi chỉnh sửa :

            <img src=https://i.imgur.com/OiKLGQE.png>

## **5) Test performance giữa 2 định dạng**
- **VD :** Thực hiện các lệnh sau :
    ```
    # dd if=/var/lib/libvirt/images/centos7-02.qcow2 of=test1 bs=10k count=100000
    19+1 records in
    19+1 records out
    197120 bytes (197 kB) copied, 0.00151936 s, 130 MB/s

    # dd if=/var/lib/libvirt/images/centos7-02.raw of=test2 bs=10k count=100000
    100000+0 records in
    100000+0 records out
    1024000000 bytes (1.0 GB) copied, 4.05949 s, 252 MB/s
    ```
    ```
    # dd if=/dev/zero of=/var/lib/libvirt/images/centos7-02.qcow2 bs=10k count=100000
    100000+0 records in
    100000+0 records out
    1024000000 bytes (1.0 GB) copied, 1.39972 s, 732 MB/s

    # dd if=/dev/zero of=/var/lib/libvirt/images/centos7-02.raw bs=10k count=100000
    100000+0 records in
    100000+0 records out
    1024000000 bytes (1.0 GB) copied, 1.24782 s, 821 MB/s
    ```




