# Migrate VMs
## **1) Migrate qua Linux Bridge**
## **2) Live Migrate**
- Trong quá trình vận hành để phục vụ cho việc bảo trì và nâng cấp hệ thống chúng ta cần chuyển các VM từ host này sang host khác. Với các VM đang chạy các ứng dụng quan trọng chúng ta không thể tắt nó đi trong quá trình chuyển. Trên KVM việc live migrate sẽ đảm bảo được các yêu cầu này .
### **Mô hình**
<img src=https://i.imgur.com/Jk9Gfgh.png>

### **Cơ chế thực hiện**
- Cơ chế cơ bản của live-migrate:
    - Về cơ bản cơ chế di chuyển VM khi VM vẫn đang hoạt động. Quá trình trao đổi diễn ra nhanh các phiên làm việc kết nối hầu như không cảm nhận được sự gián đoạn nào. Quá trình Live Migrate được diễn ra như sau:
        - **B1 :** 1 ảnh chụp ban đầu của VM cần chuyển trên host KVM162 được chuyển sang VM trên host KVM163. Trong trường hợp người dùng đang truy cập VM tại host KVM162 thì những sự thay đổi và hoạt động trên host KVM162 vẫn diễn ra bình thường, tuy nhiên những thay đổi này sẽ không được ghi nhận.
        - **B2 :** Những thay đổi của VM trên host KVM162 được đồng bộ liên tục đến host KVM163.
        - **B3 :** Khi đã đồng bộ xong thì VM trên host KVM162 sẽ offline và các phiên truy cập trên host KVM162 được chuyển sang host KVM163.
### **Thực hiện**
- **B1 :** Tắt firewalld và SELinux của cả 3 máy :
    ```
    # systemctl stop firewalld 
    # setenforce 0
    ```
- **B2 :** Phân giải đúng domain name cho 2 máy **KVM01** và **KVM02** để chúng có thể thấy nhau trên mạng LAN :
    - Trên **KVM01** :
        ```
        # vi /etc/hosts
        ```
        - Chỉnh sửa với nội dung sau :
            ```
            kvm01
            192.168.5.20  kvm02.local kvm02
            ```
        - Reboot lại máy
    - Trên **KVM02** :
        ```
        # vi /etc/hosts
        ```
        - Chỉnh sửa với nội dung sau :
            ```
            kvm02
            192.168.5.10  kvm01.local kvm01
            ```
        - Reboot lại máy
- **B3 :** Cài đặt NFS trên **NFS Server** :
    - Cài đặt NFS :
        ```
        # yum install -y nfs-utils nfs-utils-lib
        ```
    - Chọn 1 thư mục để làm thư mục share hoặc tạo mới 1 thư mục. Ở đây, ta tạo 1 thư mục `/root/storage`
        ```
        # mkdir /root/storage
        ```
    - Chia sẻ thư mực này với các máy KVM host bằng cách ghi các thông tin như sau vào trong file `/etc/exports` :
        ```
        # vi /etc/exports
        ```
        - Thêm vào nội dung sau :
        ```
        /root/storage 192.168.5.10/24(rw,sync,no_root_squash)
        /root/storage 192.168.5.20/24(rw,sync,no_root_squash)
        ```
    - Cập nhật lại file vừa chỉnh sửa :
        ```
        # exportfs -a
        ```
    - Khởi động dịch vụ NFS :
        ```
        # systemctl start nfs
        # systemctl enable nfs
        ```
- **B4 :** Cài đặt NFS trên 2 máy KVM01 và KVM02 :
    - Cài đặt NFS :
        ```
        # yum install -y nfs-utils nfs-utils-lib
        ```
    - Mount thư mục chứa máy ảo với thư mục đã share. Lưu ý: địa chỉ IP đúng với IP của NFS server
        ```
        # mount 192.168.5.30:/root/storage/ /var/lib/libvirt/images/
        ```
- **B5 :** Chỉnh sửa thông tin máy ảo CentOS7-01 trên KVM01 để tránh mất dữ liệu trong quá trình migrate :
    ```
    # virsh edit CentOS7-01
    ```
    <img src=https://i.imgur.com/P2tyIKJ.png>
- **B6 :** Kết nối emu giữa 2 máy KVM :
    - Để có thể live migrate giữa hai host thì hai host này cần phải kết nối được với nhau. Để làm được việc này ta thực hiện các bước sau ở trên cả hai máy host KVM :
        ```
        # sed -i 's/#listen_tls = 0/listen_tls = 0/g' /etc/libvirt/libvirtd.conf 
        # sed -i 's/#listen_tcp = 1/listen_tcp = 1/g' /etc/libvirt/libvirtd.conf
        # sed -i 's/#tcp_port = "16509"/tcp_port = "16509"/g' /etc/libvirt/libvirtd.conf
        # sed -i 's/#listen_addr = "192.168.5.1"/listen_addr = "0.0.0.0"/g' /etc/libvirt/libvirtd.conf
        # sed -i 's/#auth_tcp = "sasl"/auth_tcp = "none"/g' /etc/libvirt/libvirtd.conf
        # sed -i 's/#LIBVIRTD_ARGS="--listen"/LIBVIRTD_ARGS="--listen"/g' /etc/sysconfig/libvirtd
        ```
    - Restart lại libvirtd trên cả hai máy :
        ```
        # systemctl restart libvirtd
        ```
- **B7 :** Kiểm tra lại VM cần migrate trên máy **KVM01** :
    ```
    # virsh list
    ```
    <img src=https://i.imgur.com/oRVaAn3.png>

- **B8 :** Thực hiện live migrate :
    ```
    # virsh migrate --live CentOS7-01 qemu+tcp://192.168.5.20/system
    ```
    <img src=https://i.imgur.com/rR2iKtv.png>
    
    - Nếu có thông báo như trên thì ta tiếp tục thực hiện lệnh lại lần nữa với option  `--unsafe` :
    ```
    # virsh migrate --live --unsafe CentOS7-01 qemu+tcp://192.168.5.20/system
    ```