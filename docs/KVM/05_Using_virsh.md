# Sử dụng `virsh`
## **1) Libvirt và `virsh`**
- **Libvirt** là một bộ các phần mềm mà cung cấp các cách thuận tiện để quản lý máy ảo và các chức năng của ảo hóa. Những phần mềm này bao gồm một thư viện API daemon (`libvirtd`) và các gói tiện tích giao diện dòng lệnh (`virsh`) .
- `virsh` là một tools kiểm soát và thực hiện hành động với các máy ảo .
## **2) Các lệnh cơ bản với `virsh`**
- Show tất cả các máy ảo đã được cài đặt :
    ```
    # virsh list --all
    ```
    <img src=https://i.imgur.com/R8kJLUL.png>

- Hiển thị các máy ảo đang hoạt động :
    ```
    # virsh list
    ```
    <img src=https://i.imgur.com/m7ahyaj.png>

- Khởi động máy ảo :
    ```
    # virsh start CentOS7-01
    ```
    <img src=https://i.imgur.com/NFodMvH.png>

- Tắt máy ảo :
    ```
    # virsh shutdown CentOS7-01
    ```
    <img src=https://i.imgur.com/kBKbZQP.png>

- Khởi động lại máy ảo :
    ```
    # virsh reboot CentOS7-01
    ```
    <img src=https://i.imgur.com/zofG4HI.png>

- Suspend máy ảo :
    ```
    # virsh suspend CentOS7-01
    ```
    <img src=https://i.imgur.com/3ZWdQ14.png>

- Resume lại máy ảo sau khi suspend :
    ```
    # virsh resume CentOS7-01
    ```
    <img src=https://i.imgur.com/IKeMiXs.png>

- Chỉnh sửa thông số máy ảo :
    ```
    # virsh edit CentOS7-01
    ```
    <img src=https://i.imgur.com/yWLZ3vB.png>

    - Sau khi chỉnh sửa , cập nhật lại cấu hình :   
        ```
        # virsh define /etc/libvirt/qemu/CentOS7-01.xml
        ```
        <img src=https://i.imgur.com/PhKLdZw.png>

- Xóa máy ảo :
    ```
    # virsh destroy CentOS7-01
    # virsh undefine /etc/libvirt/qemu/CentOS7-01.xml
    ```
    - Sau khi xóa máy ảo, ta có thể tùy chọn có xóa image không . Nếu xóa image :
        ```
        # rm -rf /var/lib/libvirt/images/centos7-01.qcow2
        ```
## **3) Các lệnh kiểm tra thông tin**
- Xem file `.xml` của máy ảo :
    ```
    # virsh dumpxml CentOS7-01
    ```
    <img src=https://i.imgur.com/BZvXe2M.png>

- Xem thông tin máy ảo :
    ```
    # virsh dominfo CentOS7-01
    ```
    <img src=https://i.imgur.com/3CrUIVz.png>

- Kiểm tra các cổng của máy ảo :
    ```
    # virsh domiflist CentOS7-01
    ```
    <img src=https://i.imgur.com/mJg4oQ1.png>

## **4) Các thao tác Clone**
> Trước khi clone cần **SHUTDOWN** máy ảo trước!
- Clone tự động :
    ```
    # virt-clone --original=CentOS7-01 --auto-clone
    ```
    <img src=https://i.imgur.com/j53lAcm.png>

- Clone custom tên máy clone :
    ```
    # virt-clone --original=CentOS7-01 --name=CentOS7-03 --auto-clone
    ```
    <img src=https://i.imgur.com/DLzZDMe.png>
    
- Clone custom tên máy clone và disk image :
    ```
    # virt-clone --original=CentOS7-01 --name=CentOS7-04 --file=/var/lib/libvirt/images/centos7-04.qcow2
    ```
    <img src=https://i.imgur.com/QsGFUd8.png>

## **5) Các thao tác Snapshot**
- Tạo snapshot :
    ```
    # virsh snapshot-create-as --domain CentOS7-01 --name "Begin" --description "ban khoi tao"
    ```
    <img src=https://i.imgur.com/mm6fPJl.png>

- Show các bản snapshot đã tạo :
    ```
    # virsh snapshot-list CentOS7-01
    ```
    <img src=https://i.imgur.com/vgWrwM7.png>

- Xem thông tin chi tiết bản snapshot đã tạo :
    ```
    # virsh snapshot-info CentOS7-01 --snapshotname "Begin"
    ```
    <img src=https://i.imgur.com/kCBX9wM.png>

- Reverse lại 1 bản snapshot đã tạo :
    ```
    # virsh snapshot-revert CentOS7-01 --snapshotname "Begin"
    ```
- Xóa một bản snapshot đã tạo :
    ```
    # virsh snapshot-delete CentOS7-01 --snapshotname "Begin"
    ```
    <img src=https://i.imgur.com/FZl4vFU.png>

## **6) Console máy ảo**
- **B1 :** Khởi động dịch vụ **TRÊN CÁC MÁY ẢO** để cho phép máy chủ KVM console không cần mật khẩu :
    ```
    # systemctl start serial-getty@ttyS0.service
    # systemctl enable serial-getty@ttyS0.service
    ```
- **B2 :** Trên host KVM, thực hiện lệnh :
    ```
    # virsh console CentOS7-01
    ```
    <img src=https://i.imgur.com/kvXdQwa.png>
    
    > Gõ `Ctrl` + `]` để thoát phiên console
    
    > Từ phiên console thứ 2 sẽ không cần phải đăng nhập user và password nữa