# HA Cluster
## **1) Giới thiệu**
- **High-Availability Cluster** hay còn gọi là **Failover-cluster** (hoặc **active-passive cluster**) là một trong những loại cluster phổ biến nhất trong môi trường production. Loại cluster này cung cấp tính sẵn sàng cho service ngay cả khi 1 node cluster bị down. Nếu server chạy ứng dụng bị sập vì một số lí do như (lỗi phần cứng), cluster software (**Pacemaker**) sẽ khởi động lại ứng dụng ở trên node còn lại.
- **High-Availability** được sử dụng chính cho database, các ứng dụng tùy chỉnh, hoặc cho file sharing. Tính năng fail-over không chủ khởi động 1 ứng dụng. Nó còn hỗ trợ một loạt các hành động liên quan như mount filesystems, cấu hình mạng và khởi động các ứng dụng liên quan 
## **2) Các bước cài đặt**
### **Mô hình**
- **CentOS7/RHEL7** hỗ trợ fail-over cluster sử dụng `pacemaker`. Mô hình cài đặt sẽ ví dụ về việc cấy hình **Apache Web Service** làm ứng dụng cần ***high-availability*** . Đối với **shared storage** ta sẽ sử dụng **iSCSI storage**.

    <img src=https://i.imgur.com/CQEzx10.png>

### **2.1) Cấu hình Shared Storage**
- **Shared storage** là một trong các thành phần quan trọng của **HA Cluster** và nó sẽ chứa dữ liệu của ứng dụng đang chạy. Tất cả các node trong cluster sẽ phải truy cập vào **shared storage** để lấy dữ liệu. **SAN storage** là loại **shared storage** được sử dụng rộng rãi nhất trong môi trường production. Tuy nhiên trong ví dụ này ta sẽ sử dụng **iSCSI storage** để minh họa.
- **B1 :** Cài đặt các package cần thiết:
    - Trên **iSCSI storage**, cài đặt `targetcli` :
        ```
        # yum install targetcli -y
        ```
    - Trên các cluster node, cài đặt `iscsi-initiator-utils` để kết nối đến **iSCSI storage** :
        ```
        # yum install iscsi-initiator-utils -y
        ```
- **B2 :** Setup Disk :
    - Ta sẽ tạo ví dụ `10GB` LVM disk trên **iSCSI storage** để sử dụng làm **shared storage** cho các cluster node. 
    - Kiểm tra disk `sdb` vừa thêm :
        ```
        # fdisk -l | grep -i sd
        ```
        ```
        Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
        /dev/sda1   *        2048     2099199     1048576   83  Linux
        /dev/sda2         2099200    83886079    40893440   8e  Linux LVM
        Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
        ```
    - Tạo Disk LVM :
        ```
        # pvcreate /dev/sdb
        # vgcreate vg_iscsi /dev/sdb
        # lvcreate -l 100%FREE -n lv_iscsi vg_iscsi
        ```
- **B3 :** Khởi tạo **shared storage** :
    - Trên các node cluster, thực hiện lệnh sau để lấy `InitiatorName` :
        ```
        # cat /etc/iscsi/initiatorname.iscsi
        ```
        - Trên **Node 1** :
            ```
            InitiatorName=iqn.1994-05.com.redhat:bb246ca48e18
            ```
        - Trên **Node 2** :
            ```
            InitiatorName=iqn.1994-05.com.redhat:de4a85f63722
            ```
    - Trên node **Storage**, thực hiện gõ các lệnh sau trong **iSCSI CLI** :
        ```
        # targetcli
        ```
        ```
        Warning: Could not load preferences file /root/.targetcli/prefs.bin.
        targetcli shell version 2.1.51
        Copyright 2011-2013 by Datera, Inc and others.
        For help on commands, type 'help'.

        /> cd /backstores/block
        /backstores/block> create iscsi_shared_storage /dev/vg_iscsi/lv_iscsi
        Created block storage object iscsi_shared_storage using /dev/vg_iscsi/lv_iscsi.
        /backstores/block> cd /iscsi
        /iscsi> create
        Created target iqn.2003-01.org.linux-iscsi.rhel7-03.x8664:sn.f8a1136eff70.
        Created TPG 1.
        Global pref auto_add_default_portal=true
        Created default portal listening on all IPs (0.0.0.0), port 3260.
        /iscsi> cd iqn.2003-01.org.linux-iscsi.rhel7-03.x8664:sn.f8a1136eff70/tpg1/acls
        /iscsi/iqn.20...f70/tpg1/acls> create iqn.1994-05.com.redhat:bb246ca48e18
        Created Node ACL for iqn.1994-05.com.redhat:bb246ca48e18
        /iscsi/iqn.20...f70/tpg1/acls> create iqn.1994-05.com.redhat:de4a85f63722
        Created Node ACL for iqn.1994-05.com.redhat:de4a85f63722
        /iscsi/iqn.20...f70/tpg1/acls> cd /iscsi/iqn.2003-01.org.linux-iscsi.rhel7-03.x8664:sn.f8a1136eff70/tpg1/luns
        /iscsi/iqn.20...f70/tpg1/luns> create /backstores/block/iscsi_shared_storage
        Created LUN 0.
        Created LUN 0->0 mapping in node ACL iqn.1994-05.com.redhat:de4a85f63722
        Created LUN 0->0 mapping in node ACL iqn.1994-05.com.redhat:bb246ca48e18
        /iscsi/iqn.20...f70/tpg1/luns> cd /
        /> ls
        o- / ............................................................................................................. [...]
        o- backstores .................................................................................................. [...]
        | o- block ...................................................................................... [Storage Objects: 1]
        | | o- iscsi_shared_storage .................................. [/dev/vg_iscsi/lv_iscsi (10.0GiB) write-thru activated]
        | |   o- alua ....................................................................................... [ALUA Groups: 1]
        | |     o- default_tg_pt_gp ........................................................... [ALUA state: Active/optimized]
        | o- fileio ..................................................................................... [Storage Objects: 0]
        | o- pscsi ...................................................................................... [Storage Objects: 0]
        | o- ramdisk .................................................................................... [Storage Objects: 0]
        o- iscsi ................................................................................................ [Targets: 1]
        | o- iqn.2003-01.org.linux-iscsi.rhel7-03.x8664:sn.f8a1136eff70 ............................................ [TPGs: 1]
        |   o- tpg1 ................................................................................... [no-gen-acls, no-auth]
        |     o- acls .............................................................................................. [ACLs: 2]
        |     | o- iqn.1994-05.com.redhat:bb246ca48e18 ...................................................... [Mapped LUNs: 1]
        |     | | o- mapped_lun0 ...................................................... [lun0 block/iscsi_shared_storage (rw)]
        |     | o- iqn.1994-05.com.redhat:de4a85f63722 ...................................................... [Mapped LUNs: 1]
        |     |   o- mapped_lun0 ...................................................... [lun0 block/iscsi_shared_storage (rw)]
        |     o- luns .............................................................................................. [LUNs: 1]
        |     | o- lun0 ............................. [block/iscsi_shared_storage (/dev/vg_iscsi/lv_iscsi) (default_tg_pt_gp)]
        |     o- portals ........................................................................................ [Portals: 1]
        |       o- 0.0.0.0:3260 ......................................................................................... [OK]
        o- loopback ............................................................................................. [Targets: 0]
        /> saveconfig
        Configuration saved to /etc/target/saveconfig.json
        /> exit
        Global pref auto_save_on_exit=true
        Last 10 configs saved in /etc/target/backup/.
        Configuration saved to /etc/target/saveconfig.json
        ```
        - Khởi động dịch vụ `target` :
            ```
            # systemctl enable target
            # systemctl restart target
            ```
        - Cấu hình `firewalld` cho phép traffic của `iSCSI` :
            ```
            # firewall-cmd --permanent --add-port=3260/tcp
            # firewall-cmd --reload
            ```
- **B4 :** Discover **shared storage**:
    - Trên cả 2 cluster node, discover `target` bằng lệnh sau:
        ```
        # iscsiadm -m discovery -t st -p 192.168.5.103
        ```
        > `192.168.5.103` là IP của **shared storage**
        - Output :
            ```
            192.168.5.103:3260,1 iqn.2003-01.org.linux-iscsi.rhel7-03.x8664:sn.f8a1136eff70
            ```
    - Login vào `target` :
        ```
        # iscsiadm -m node -T iqn.2003-01.org.linux-iscsi.rhel7-03.x8664:sn.f8a1136eff70 -p 192.168.5.103 -l
        ```
        - Output :
            ```
            Logging in to [iface: default, target: iqn.2003-01.org.linux-iscsi.rhel7-03.x8664:sn.f8a1136eff70, portal: 192.168.5.103,3260] (multiple)
            Login to [iface: default, target: iqn.2003-01.org.linux-iscsi.rhel7-03.x8664:sn.f8a1136eff70, portal: 192.168.5.103,3260] successful.
            ```
    - Restart và Enable dịch vụ `iscsid` :
        ```
        # systemctl restart iscsid
        # systemctl enable iscsid
        ```
    - Check lại trên các node cluster đã xuất hiện **shared storage** dưới dạng ổ `/dev/sdb` :
        ```
        # fdisk -l | grep -i sd
        ```
        - Output :
            ```
            Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
            /dev/sda1   *        2048     2099199     1048576   83  Linux
            /dev/sda2         2099200    83886079    40893440   8e  Linux LVM
            Disk /dev/sdb: 10.7 GB, 10733223936 bytes, 20963328 sectors
            ```
- **B5 :** Phân giải tên miền cho các node cluster :
    ```
    # vi /etc/hosts
    ```
    - Thêm vào các dòng sau:
        ```
        192.168.5.101 rhel7-01
        192.168.5.102 rhel7-02
        ```
- **B6 :** Tạo một filesystem LVM cho **Apache Web server** để lưu trữ dữ liệu website (trên 1 node cluster):
    ```
    # pvcreate /dev/sdb
    # vgcreate vg_apache /dev/sdb
    # lvcreate -n lv_apache -l 100%FREE vg_apache
    # mkfs.ext4 /dev/vg_apache/lv_apache
    ```
https://www.itzgeek.com/how-tos/linux/centos-how-tos/configure-high-avaliablity-cluster-on-centos-7-rhel-7.html