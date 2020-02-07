# QEMU
## **1) Giới thiệu**
- **Quick Emulator** ( **QEMU** ) là thành phần chính của bộ công nghệ ảo hóa **QEMU/KVM** .
- Nó cung cấp giả lập về phần cứng cũng như các mô phỏng về CPU .
- **QEMU** chạy trong không gian của người dùng và không cần kernel hay driver thì nó vẫn có thể cung cấp quá trình mô phỏng nhanh .
- **QEMU** hỗ trợ 2 chế độ hoạt động :
    - **Full system emulation** : **QEMU** sẽ giả lập toàn bộ hệ thống của máy tính , bao gồm cả loại CPU và các thiết bị ngoại vi .
    - **User mode Emulation** : **QEMU** có thể chạy một tiến trình được biên dịch dựa trên 1 CPU ảo .
- Source Code : https://www.qemu.org/download/#source
- Phiên bản mới nhất : `4.2.0`
## **2) Cài đặt và cấu hình QEMU**
### **2.1) Cài đặt trên Ubuntu**
- **B1 :** Update các package sẵn có :
    ```sh
    $ sudo apt-get update
    ```
- **B2 :** Cài đặt **QEMU/KVM** :
    ```sh
    $ sudo apt-get install -y qemu
    ```
### **2.2) Cài đặt trên CentOS**
- **B1 :** Update các package sẵn có :
    ```
    # yum update -y
    ```
- **B2 :** Cài đặt **QEMU/KVM** :
    ```
    # yum install -y qemu-kvm
    ```
### **2.3) Cài đặt từ source**
- **B1 :** Download source **QEMU** từ internet và lưu vào thư mục `/var/tmp` :
    ```
    # cd /var/tmp
    # wget https://download.qemu.org/qemu-4.2.0.tar.xz
    ```
- **B2 :** Giải nén file vừa tải về :
    ```
    # tar -xvjf qemu-4.2.0.tar.xz
    ```
- **B3 :** Biên dịch và cài đặt :
    ```
    # cd qemu-4.2.0
    # ./configure
    # make && make install
    ```
> Các công cụ được thêm vào sau khi cài **QEMU** :

<img src=https://i.imgur.com/PSvs5eJ.png>

## **3) Quản lý các `disk-images`**
- Để chạy các máy ảo, **QEMU** cần các **images** để lưu trữ hệ thống filesystem của các **guest OS** .
- Các **images** chính là 1 dạng file đại diện cho filesystem của **guest OS** nằm trong **virtual disk** .
- **QEMU** cung cấp đa dạng các **images** và các công cụ để tạo và quản lý chúng .
- Sử dụng công cụ `qemu-img` để quản lý các **disk-images** .
### **3.1) Kiểm tra các định dạng images được hỗ trợ** :
- Sử dụng lệnh :
    ```
    # qemu-img -h | grep Supported
    ```
    ```
    Supported formats: blkdebug blkreplay blkverify bochs cloop dmg file ftp ftps host_cdrom host_device http https iscsi iser luks nbd null-aio null-co parallels qcow qcow2 qed quorum raw rbd replication sheepdog throttle vdi vhdx vmdk vpc vvfat
    ```
- Một số định dạng **images** phổ biến :
    - `raw` : đây là định dạng mặc định và cũng có thể coi là định dạng nhanh nhất .
    - `qcow2` : là một trong các định dạng nhiều tính năng nhất . Nó cung cấp tính năng snapshot VM, nén, mã hóa ...
    - `qcow` : là một định dạng cũ của **QEMU** .
    - `dmg` : là định dạng dành cho MAC OS 
    - `nbd` : viết tắt của ***network block device*** , thường được sử dụng để kết nối đến các thiết bị storage đầu xa .
    - `vdi` : định dạng được sử dụng bởi **Oracle VirtualBox**
    - `vmdk` : định dạng được sử dụng bởi **VMware**
    - `vhdx` : định dạng được sử dụng bởi **Hyper-V**
> Hai định dạng phổ biến và nên dùng nhất là `raw` và `qcow2` .
### **3.2) Tạo images**
- Tiện ích `qemu-img` cho phép tạo , chuyển đổi và chỉnh sửa **images** .
- Cú pháp :
    ```
    # qemu-img create -f [image_format] [image_name] [image_size]
    ```
    - Trong đó :
        - `-f [image format]` : chỉ định định dạng của **image** .
- **VD :** Tạo 1 **raw image** tên là `debian.img` với kích cỡ `10GB` :
    ```
    # qemu-img create -f raw debian.img 10G
    Formatting 'debian.img', fmt=raw size=10737418240
    ```
- Kiểm tra **image** vừa được tạo :
    ```
    # ls -l debian.img
    -rw-r--r-- 1 root root 10737418240 Thg 2  5 16:30 debian.img
    ```
- Kiểm tra thông tin chi tiết về **image** được tạo :
    ```
    # qemu-img info debian.img
    image: debian.img
    file format: raw
    virtual size: 10G (10737418240 bytes)
    disk size: 0
    ```
### **3.3) Phân chia các partition**
- Sau khi tạo một image trống , tiếp theo sẽ tạo các partition và filesystem trên nó để chuẩn bị cho việc cài đặt OS .
- **B1 :** Load kernel module `nbd` :
    ```
    # modprobe nbd
    ```
- **B2 :** Sử dụng công cụ `qemu-nbd`, liên kết file image trống vừa tạo với block `/dev/nbd0` :
    ```
    # qemu-nbd --format=raw --connect=/dev/nbd0
    ```
- **B3 :** Tạo ra 2 partition cho block `/dev/nbd0` , 1 dùng cho swap có dung lượng là `1G` (ID là `82`), 1 dùng cho thư mục `/` của **guest OS**  sẽ chiếm phần dung lượng còn lại (ID mặc định sẽ là `83`) :
    ```
    # sfdisk /dev/nbd0 <<EOF
    >,1G, 82
    >;
    >EOF
    ```
    ```
    Checking that no-one is using this disk right now ... OK

    Disk /dev/nbd0: 10 GiB, 10737418240 bytes, 20971520 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0xffa8a120

    /dev/nbd0p1: Created a new partition 1 of type 'Linux swap / Solaris' and of size 1 GiB.
    /dev/nbd0p2: Created a new partition 2 of type 'Linux' and of size 9 GiB.
    /dev/nbd0p3: Done.

    New situation:
    Disklabel type: dos
    Disk identifier: 0xab390a3f

    Device      Boot   Start      End  Sectors Size Id Type
    /dev/nbd0p1         2048  2099199  2097152   1G 82 Linux swap / Solaris
    /dev/nbd0p2      2099200 20971519 18872320   9G 83 Linux

    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Syncing disks.
    ```
- **B4 :** Kiểm tra lại các block vừa tạo :
    ```
    # ls -l /dev/nbd0*
    ```
    ```
    brw-rw---- 1 root disk 43, 0 Thg 2  5 16:59 /dev/nbd0
    brw-rw---- 1 root disk 43, 1 Thg 2  5 16:59 /dev/nbd0p1
    brw-rw---- 1 root disk 43, 2 Thg 2  5 16:59 /dev/nbd0p2
    ```
- **B5 :** Tạo phân vùng swap :
    ```
    # mkswap /dev/nbd0p1
    ```
    ```
    Setting up swapspace version 1, size = 1024 MiB (1073737728 bytes)
    no label, UUID=26fe19af-d991-4869-b928-ac22bb8efe4c
    ```
- **B6 :** Format phân vùng root thành định dạng `ext4` :
    ```
    # mkfs.ext4 /dev/nbd0p2
    ```
    ```
    mke2fs 1.44.1 (24-Mar-2018)
    Discarding device blocks: failed - Input/output error
    Creating filesystem with 2359040 4k blocks and 589824 inodes
    Filesystem UUID: 6a6854d9-a96b-406d-97fa-c22fc0e2c31a
    Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

    Allocating group tables: done                            
    Writing inode tables: done                            
    Creating journal (16384 blocks): done
    Writing superblocks and filesystem accounting information: done 
    ```
### **3.4) Cài đặt OS**
#### **3.4.1) Download images from Internet**
- Các **images** chính thức từ Internet :
    - **Ubuntu** : https://uec-images.ubuntu.com/releases/
    - **CentOS** : https://cloud.centos.org/centos/
    - **Debian** : https://cdimage.debian.org/cdimage/openstack/
    - **Fedora** : https://alt.fedoraproject.org/cloud/
    - **openSUSE** : https://download.opensuse.org/repositories/Cloud:/Images:/
- **B1 :** Download **image** về máy :
    ```
    # wget https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud-1907.qcow2
    ```