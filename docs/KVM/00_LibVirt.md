# Sử dụng Libvirt để quản lý KVM
## **1) Cài đặt `libvirt`**
- **B1 :** Update các package sẵn có :
    ```
    # apt-get update                  (Ubuntu)
    ```
    hoặc
    ```
    # yum install libvirt             (CentOS)
    ```
- **B2 :** Cài đặt `libvirt-bin` :
    ```
    # apt-get install -y libvirt-bin
    ```
    - Kiểm tra lại xem đã có `libvirtd` daemon chưa :
        ```
        # pgrep -l libvirtd
        61913 libvirtd
        ```
    - Sau khi cài đặt `libvirt` có 2 file cần chú ý :
        - `/etc/libvirt` : thư mục chứa các file cấu hình của `libvirt` .
            - `/etc/libvirt/libvirt.conf` : file cấu hình client-side được sử dụng cho các option của lệnh `virsh` .
            - `/etc/libvirt/libvirtd.conf` : file cấu hình mặc định server-side , cung cấp các option về bảo mật, giới hạn request, kiểm soát log .
            - `/etc/libvirt/qemu.conf` : file cấu hình chính cho **QEMU** driver mà `libvirt` sử dụng . Có thể cấu hình thêm địa chỉ VNC Server, security driver,...
            - `/etc/libvirt/qemu/` : thư mục chứa các file XML định nghĩa các máy ảo được tạo ra .
                - `/etc/libvirt/qemu/networks/` : thư mục chứa cá file cấu hình networks
- **B3 :** Disable security driver trong file cấu hình **QEMU** :
    ```
    # vi /etc/libvirt/qemu.conf
    ...
    security_driver = "none"
    ...
    ```
    <img src=https://i.imgur.com/MR7v6ky.png>
- **B4 :** Khởi động lại `libvirtd` daemon :
    ```
    # /etc/init.d/libvirt-bin restart    (Ubuntu)
    ```
    hoặc
    ```
    # service libvirt restart            (CentOS)
    ```
## **2) Định nghĩa các máy ảo (KVM instances)**
- **B1 :** Liệt kê các máy ảo có trên host :
    ```
    # virsh list --all
     Id    Name                           State
    ----------------------------------------------------

    ```
- **B2 :** Tạo máy ảo bằng lệnh `virt-install` :
    ```
    # virt-install \
    > --name=Centos7-may4 \
    > --vcpus=1 \
    > --memory=1024 \
    > --cdrom=CentOS-7-x86_64-Minimal-1804.iso \
    > --disk=may4,size=10 \
    > --os-variant=rhel7 \
    > --network bridge=virbr1
    ```
    - Trong đó :
        - `--name` đặt tên cho máy ảo định tạo
        - `--vcpus` là tổng số CPU đinhj tạo cho máy ảo
        - `--memory` chỉ ra dung lượng RAM cho máy ảo (tính bằng MB)
        - `--cdrom` sau đó chỉ ra đường dẫn đến file ISO. Nếu muốn boot bằng cách khác ta dùng option
        - `--location` sau đó chỉ ra đường dẫn đến file (có thể là đường dẫn trên internet).
        - `--disk` chỉ ra vị trí lưu disk của máy ảo. size chỉ ra dung lượng disk của máy ảo(tính bằng GB)
        - `--os-variant` chỉ ra kiểu của HĐH của máy ảo đang tạo. Option này có thể chỉ ra hoặc không nhưng nên sử dụng nó vì nó sẽ cải thiện hiệu năng của máy ảo. Nếu bạn không biết HĐH hành của mình thuộc loại nào bạn có thể tìm kiếm thông tin bằng cách dùng lệnh osinfo-query os
        - `--network` chỉ ra cách kết nối mạng của máy ảo. Trên đây là một số option cơ bản để tạo máy ảo. Bạn có thể tìm hiểu thêm bằng cách sử dụng lệnh `virt-install --help`