# Công nghệ Linux Bridge
## **1) Giới thiệu**
- Đối với chế độ **bridge**, KVM sử dụng công nghệ **Linux Bridge** .
- **Linux Bridge** là một phần mềm được tích hợp trong nhân Linux để giải quyết vấn đề ảo hóa phần Network trong các máy vật lý .
- Về mặt logic, **Linux Bridge** tạo ra một con switch ảo để các VM kết nối vào và có thể nói chuyện với nhau cũng như sử dụng để ra ngoài mạng .

    <p align=center><img src=https://i.imgur.com/U2ritvT.png width=80%></p>

## **2) Kiến trúc** 

<p align=center><img src=https://i.imgur.com/oXlZTem.png width=70%></p>

- Trong kiến trúc trên có các thành phần:
    - **Tap** : có thể hiểu nó là một giao diên mạng để các máy ảo có thể giao tiếp được với bridge và nó nằm trong nhân kernel. Tap hoat động ở lớp 2 trong mô hình OSI
    - **vfs** ( virtual file system): tạo 1 phân vùng để nhận gói tin forward data từ máy ảo thông qua forward database.
    - **fd** (forward database): là cổng giao tiếp chuyển tiếp dữ liệu giữa máy ảo với bridge.
    - **Bridge** : có chức năng giống với swtich layer 2.
    - **Port** : có chức năng tương đương với port của switch thật.
## **3) Chức năng của một switch ảo do Linux bridge tạo ra**
- **STP** : là tính năng chống loop gói tin trong switch
- **VLAN** : là tính năng rất quan trọng trong một switch
- **FDB** : là tính năng chuyển gói tin theo database được xây dựng giúp tăng tốc độ của switch .
- Các lệnh làm việc với **bridge** :
    - Tạo **bridge** :
        ```
        # brctl addbr bridge_name
        ```
    - Gán card cho **bridge** :
        ```
        # brctl addif bridge_name card_name
        ```
    - Hiển thị tất cả các của **bridge** :
        ```
        # brctl show
        ```
    - Show riêng thông tin của 1 bridge :
        ```
        # brctl show bridge_name
        ```
    - Ngắt kết nối card khỏi **bridge** :
        ```
        # brctl delif bridge_name card_name
        ```
    - Xóa bridge :
        ```
        # brctl delbr bridge_name
        ```
    - Show forwarding table của bridge :
        ```
        # brctl showmacs bridge_name
        ```
## **4) Cách cài đặt Bridge** :
### **Mô hình**
<img src=https://i.imgur.com/APGZ4Mw.png>

### **Thực hiện**
- **B1 :** Trên máy KVM, tạo **bridge** :
    ```
    # nmcli con add type bridge autoconnect yes con-name bridge01 ifname bridge01 
    ```
    <img src=https://i.imgur.com/FqaxPtS.png>

    > Trong đó "`bridge01`" là tên card bridge muốn tạo

- **B2 :** Đặt IP cho **bridge** ( IP cùng dải mạng với card `ens33` ) :
    ```
    # nmcli con mod bridge01 ipv4.addresses 192.168.5.3/24 ipv4.method manual
    ```
- **B3 :** Đặt Gateway cho **bridge** ( Gateway của card `ens33` ) :
    ```
    # nmcli con mod bridge01 ipv4.gateway 192.168.5.2
    ```
- **B4 :** Đặt DNS cho **bridge** :
    ```
    # nmcli con mod bridge01 ipv4.dns 8.8.8.8
    ```
- **B5 :** Xóa các thông số IP của card `ens33` :
    ```
    # nmcli con del ens33
    ```
    <img src=https://i.imgur.com/KRSlViH.png>

- **B6 :** Kết nối card `ens33` với **bridge** :
    ```
    # nmcli con add type bridge-slave autoconnect yes con-name ens33 ifname ens33 master bridge01
    ```
    <img src=https://i.imgur.com/t1A16FH.png>

- **B7 :** Khởi động lại máy :
    ```
    # reboot
    ```
- **B8 :** Kiểm tra lại thông tin **bridge** vừa tạo :
    ```
    # brctl show
    ```
    <img src=https://i.imgur.com/BfLMAN7.png>
- **B8 :** Bật `virt-manager`, chọn vào máy ảo CentOS7-01 , chọn thay đổi thông tin VM :
    ```
    # virt-manager
    ```

    <p align=center><img src=https://i.imgur.com/G4dSxNf.png width=80%></p>

- **B8 :** Chọn card **bridge** vừa tạo, chọn ***Apply*** :

    <p align=center><img src=https://i.imgur.com/H4dLqU7.png width=80%></p>

- **B9 :** Khởi động lại network service trên máy ảo CentOS7-01 :
    ```
    # service network restart
    ```
- **B10 :** Kiểm tra lại IP của VM để kiểm chứng rằng nó đã nhận IP của **bridge** :

    <img src=https://i.imgur.com/2jlF0pP.png>

### **Bắt gói tin**
- **TH1 :** Đứng trên máy VM1 ping đến `8.8.8.8`. Thực hiện bắt gói tin ICMP trên các điểm `eth0` của VM1, `vnet0` (tap), `bridge01`, `ens33` của KVM .
    - Trên CentOS7-01 :
        ```
        # yum install -y tcpdump
        # ping 8.8.8.8 & tcpdump -i eth0 icmp -w vm1.pcap
        ```
        - File `vm1.pcap` :
            
            <img src=https://i.imgur.com/aYGgrVy.png>
    - Trên KVM :
        ```
        # yum install -y tcpdump
        # tcpdump -i ens33 icmp -w ens33.pcap
        # tcpdump -i bridge01 icmp -w bridge01.pcap
        # tcpdump -i vnet0 icmp -w vnet0.pcap
        ```
        - File `ens33.pcap` :

            <img src=https://i.imgur.com/lUwrcHw.png>

        - File `bridge01.pcap` :

            <img src=https://i.imgur.com/h3oPbZ2.png>

        - File `vnet0.pcap` :

            <img src=https://i.imgur.com/fWgPcKC.png>

    => Như vậy ta thấy rằng card `eth0` của VM đã được gắn thằng với switch được tạo ra bởi kiểu mạng bridge của host KVM tạo ra nên ta mới thấy rằng đường đi với nó có địa chỉ đầu và cuối giống nhau dù ta có bắt gói tin ở 4 điểm khác nhau. Có nghĩa là đường đi của nó đều đi qua tất cả các điểm này đầu là VM `192.168.5.131` và điểm cuối là `8.8.8.8`
- **TH2 :** Đứng trên máy VM1 ping đến VM2 . Thực hiện bắt gói tin ICMP trên các điểm `eth0` của VM1, `vnet0` (tap), `bridge01`, `vnet1` (tap), `eth0` của VM2, `ens33` của KVM Host :
    - Trên CentOS7-01 (VM1):
        ```
        # ping 192.168.5.132 && tcpdump -i eth0 icmp -w vm1.pcap
        ```
        > Trong đó `192.168.5.132` là IP của CentOS7-02
        - File `vm1.pcap` :

            <img src=https://i.imgur.com/DHqi1FM.png>

    - Trên CentOS7-02 (VM2):
        ```
        # tcpdump -i eth0 icmp -w vm2.pcap
        ```
        - File `vm2.pcap` :

            <img src=https://i.imgur.com/bi0dDEF.png>
    - Trên KVM :
        ```
        # tcpdump -i ens33 icmp -w ens33.pcap
        # tcpdump -i bridge01 icmp -w bridge01.pcap
        # tcpdump -i vnet0 icmp -w vnet0.pcap
        # tcpdump -i vnet1 icmp -w vnet0.pcap
        ```
        - File `bridge01.pcap` :

            <img src=https://i.imgur.com/qBFT3pB.png>

        - File `vnet0.pcap` :

            <img src=https://i.imgur.com/MxmC1Pt.png>

        - File `vnet1.pcap` :

            <img src=https://i.imgur.com/yFLHVSE.png>

        - File `ens33.pcap` :

            <img src=https://i.imgur.com/AIeRfaa.png>

    => Kết luận : đường đi gói tin khi ping từ VM1 qua VM2 : 
    ```
    eth0(VM1) --> vnet0(tap) --> bridge01 --> vmnet1(tap) --> eth0(VM2)
    ```
    > Gói tin **KHÔNG ĐI QUA** card `ens33` của host KVM
    
