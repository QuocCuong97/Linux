# XML Network File
## **1) Giới thiệu về XML File**
- Giống như domain, mỗi một virtual network trong KVM sẽ được định nghĩa bằng 1 file có định dạng `.xml`, mặc định lưu tại thư mục `/var/lib/libvirt/network` .
- File virtual network mặc định của KVM là `default.xml`, trong file này chứa các thông tin giúp KVM tạo mạng ảo với chế độ mặc định là **NAT** :
    ```
    # cat /var/lib/libvirt/networks/default.xml
    ```
    <img src=https://i.imgur.com/mbwud1C.png>

- Các thành phần chính trong file network `.xml` :
    - Thẻ `<network>` :
        - **Block metadata** : 
            - Thẻ `<name>` : Tên của virtual network
            - Thẻ `<uuid>` : UUID của virtual network
        - **Block connectivity** :
            - Thẻ `<forward>` : cho biết mạng ảo sẽ được kết nối với mạng vật lí, nếu không có thẻ này, virtual network sẽ ở trạng thái `isolated` . Thuộc tính `mode` cho biết phương pháp forward dữ liệu ra ngoài, mặc định sẽ là `"nat"` .
            - Thẻ `<bridge>` : bao gồm các thuộc tính :
                - `name` : tên của bridge
                - `stp` : tùy chọn bật/tắt Spanning Tree Protocol
                - `delay` : thiết lập thời gian delay theo giây ( mặc định là `0` )
            - Thẻ `<mac>` : với thuộc tính `address` là địa chỉ mac của bridge
            - Thẻ `<ip>` : thiết lập địa chỉ ip. Bao gồm các thẻ nhỏ: `<dhcp>` (bao gồm thẻ `<range>` và `<host>`)
## **2) Cách tạo virtual network từ file `.xml`**
### **VD :** Tạo một mạng Host-only
- **B1 :** Tạo 1 file `isolated.xml` trong thư mục `/var/lib/libvirt/network` :
    ```
    # cd /var/lib/libvirt/network/
    # vi isolated.xml
    ```
    - Thêm vào đoạn sau :
        ```xml
        <network>
          <name>isolated</name>
        </network>
        ```
- **B2 :** Tiến hành define network từ file `isolated.xml` bằng câu lệnh :
    ```
    # virsh net-define isolated.xml
    ```
    <img src=https://i.imgur.com/4I23yX6.png>
- **B3 :** Kiểm tra lại các network :
    ```
    # virsh net-list --all
    ```
    <img src=https://i.imgur.com/UrtwzbB.png>
- **B4 :** Sau khi define, `libvirt` sẽ tự động add thêm một số thành phần vào file `.xml` vừa tạo và lưu nó tại `/etc/libvirt/qemu/networks/` còn file tạo ban đầu tại thư mục `/var/lib/libvirt/network` vẫn giữ nguyên . Có thể xem chi tiết bằng lệnh `` :
    ```
    # virsh net-dumpxml isolated
    ```
    <img src=https://i.imgur.com/spWENoV.png>

- **B5 :** Sửa lại file cấu hình nếu cần thiết :
    ```
    # virsh net-edit isolated
    ```
    ```xml
    <network>
      <name>isolated</name>
      <uuid>9a6f71e9-db48-461e-b916-bfe2883932e6</uuid>
      <bridge name='virbr1' stp='on' delay='0'/>
      <mac address='52:54:00:4b:09:e1'/>
      <ip address='192.168.10.1' netmask='255.255.255.0'>
        <dhcp>
          <range start='192.168.10.2' end='192.168.10.100'/>
        </dhcp>
      </ip>
    </network>
    ```
- **B6 :** Sau khi cấu hình xong, tiến hành start virtual network vừa tạo bằng câu lệnh :
    ```
    # virsh net-start isolated
    ```
    <img src=https://i.imgur.com/NOr8Zsk.png>

- **B7 :** Kiểm tra lại trên `virt-manager` :

    <p align=center><img src=https://i.imgur.com/pAn9U4p.png width=70%></p>

- **B8 :** Thử gán card mạng cho máy ảo và kiểm tra DHCP :

    <img src=https://i.imgur.com/myi45L8.png>

