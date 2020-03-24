# XML Domain file
## **1) Giới thiệu**
- **XML** ( **eXtensible Markup Language** ) là ngôn ngữ đánh dấu chung do **W3C** đề nghị để tạo ra các ngôn ngữ khác. Nó có khả năng mô tả nhiều loại dữ liệu khác nhau .
- Mục đích của **XML** là đơn giản hóa việc chia sẻ dữ liệu giữa các hệ thống khác nhau đặc biết là đối với các hệ thống được kết nối qua Internet
- Một **VM** trong **KVM** có hai thành phần chính đó là **VM's definition** được lưu dưới dạng file `.xml` mặc định ở thư mục `/etc/libvirt/qemu` và **VM's storage** lưu dưới dạng file image .
## **2) Các thành phần trong XML Domain file**
- **VD :**
    ```xml
    <domain type='kvm' id='1'>
      <name>kvm1</name>
      <memory unit='KiB'>1048576</memory>
      <vcpu placement='static'>1</vcpu>
      <os>
        <type arch='x86_64' machine='pc-i440fx-trusty'>hvm</type>
        <boot dev='hd'/>
      </os>
      <on_poweroff>destroy</on_poweroff>
      <on_reboot>restart</on_reboot>
      <on_crash>restart</on_crash>
      <devices>
        <emulator>/usr/bin/qemu-system-x86_64</emulator>
        <disk type='file' device='disk'>
          <driver name='qemu' type='raw'/>
          <source file='/tmp/debian.img'/>
          <target dev='hda' bus='ide'/>
          <alias name='ide0-0-0'/>
          <address type='drive' controller='0' bus='0' target='0' unit='0'/>
        </disk>
        <interface type='network'>
          <source network='default'/>
          <target dev='vnet0'/>
          <model type='rtl8139'/>
          <alias name='net0'/>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x03'
          function='0x0'/>
        </interface>
        <graphics type='vnc' port='5900' autoport='yes'
        listen='146.20.141.158'>
          <listen type='address' address='146.20.141.158'/>
        </graphics>
      </devices>
      <seclabel type='none'/>
    </domain>
    ```
- Trong domain bao gồm một số các thành phần như sau :
    - Name: Tên máy ảo, chỉ bao gồm kí tự chữ và số và không được trùng với những máy ảo đang chạy.
    - uuid: Mã nhận dạng quốc tế duy nhất cho máy ảo.
    - Memory: Lượng ram của máy ảo
    - Vcpu: Số cpu máy ảo được cài đặt
    - Ở mục devices có thể cho ta biết được vị trí lưu của file images
    - Ở mục interface sẽ cho ta biết được địa chỉ MAC và card để ta đang sử dụng