# XML Domain file
## **1) Giới thiệu về file XML**
- **XML** ( **eXtensible Markup Language** ) là ngôn ngữ đánh dấu chung do **W3C** đề nghị để tạo ra các ngôn ngữ khác. Nó có khả năng mô tả nhiều loại dữ liệu khác nhau .
- Mục đích của **XML** là đơn giản hóa việc chia sẻ dữ liệu giữa các hệ thống khác nhau đặc biết là đối với các hệ thống được kết nối qua Internet
- Một **VM** trong **KVM** có hai thành phần chính đó là **VM's definition** được lưu dưới dạng file `.xml` mặc định ở thư mục `/etc/libvirt/qemu` và **VM's storage** lưu dưới dạng file image .
- File domain **XML** chứa những thông tin về thành phần của máy ảo ( số vCPU, RAM, các thiết lập của I/O devices...)
- `libvirt` dùng những thông tin này để tiến hành khởi chạy tiến trình QEMU-KVM tạo máy ảo.
- Ngoài domain **XML**, KVM cũng có các file `.xml` khác để lưu các thông tin liên quan tới network, storage...
## **2) Các thành phần trong XML Domain file**
- **VD :**
    ```xml
    <domain type='kvm'>
      <name>CentOS7-02</name>
      <uuid>d21b72cd-3568-4657-b5da-bc30eebdc6c7</uuid>
      <description>None</description>
      <memory unit='KiB'>524288</memory>
      <currentMemory unit='KiB'>524288</currentMemory>
      <vcpu placement='static'>1</vcpu>
      <os>
        <type arch='x86_64' machine='pc-i440fx-rhel7.0.0'>hvm</type>
        <boot dev='hd'/>
        <boot dev='cdrom'/>
        <bootmenu enable='yes'/>
      </os>
      <features>
        <acpi/>
        <apic/>
      </features>
      <cpu mode='host-model' check='partial'>
        <model fallback='allow'/>
      </cpu>
      <clock offset='utc'/>
      <on_poweroff>destroy</on_poweroff>
      <on_reboot>restart</on_reboot>
      <on_crash>restart</on_crash>
      <devices>
        <emulator>/usr/libexec/qemu-kvm</emulator>
        <disk type='file' device='disk'>
          <driver name='qemu' type='raw' cache='directsync'/>
          <source file='/var/lib/libvirt/images/centos7-02.raw'/>
          <target dev='vda' bus='virtio'/>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x04' function='0x0'/>
        </disk>
        <disk type='file' device='cdrom'>
          <driver name='qemu' type='raw'/>
          <source file='/var/lib/libvirt/fileiso/CentOS-7-x86_64-Minimal-1908.iso'/>
          <target dev='hda' bus='ide'/>
          <readonly/>
          <address type='drive' controller='0' bus='0' target='0' unit='0'/>
        </disk>
        <controller type='usb' index='0' model='piix3-uhci'>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x2'/>
        </controller>
        <controller type='pci' index='0' model='pci-root'/>
        <controller type='ide' index='0'>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x1'/>
        </controller>
        <interface type='network'>
          <mac address='52:54:00:cb:5e:9a'/>
          <source network='default'/>
          <model type='rtl8139'/>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
        </interface>
        <serial type='pty'>
          <target type='isa-serial' port='0'>
            <model name='isa-serial'/>
          </target>
        </serial>
        <console type='pty'>
          <target type='serial' port='0'/>
        </console>
        <input type='mouse' bus='usb'>
          <address type='usb' bus='0' port='2'/>
        </input>
        <input type='keyboard' bus='usb'>
          <address type='usb' bus='0' port='1.1'/>
        </input>
        <input type='tablet' bus='usb'>
          <address type='usb' bus='0' port='1.2'/>
        </input>
        <input type='mouse' bus='ps2'/>
        <input type='keyboard' bus='ps2'/>
        <graphics type='vnc' port='-1' autoport='yes' listen='0.0.0.0'>
          <listen type='address' address='0.0.0.0'/>
        </graphics>
        <video>
          <model type='vga' vram='16384' heads='1' primary='yes'/>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x0'/>
        </video>
        <hub type='usb'>
          <address type='usb' bus='0' port='1'/>
        </hub>
        <memballoon model='virtio'>
          <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0'/>
        </memballoon>
      </devices>
    </domain>
    ```
- Trong đó :
    - Thẻ `<domain>` là thành phần không thể thiếu, chứa thuộc tính `type` cho biết hypervisor đang sử dụng :
		```xml
		<domain type='kvm'>
		```
	- **Metadata của máy ảo** :
		- Thẻ `<name>` : tên máy ảo, chỉ bao gồm kí tự chữ và số và không trùng với những máy ảo đang chạy.
			```xml
			<name>CentOS7-02</name>
			```
		- Thẻ `<uuid>` : mã nhận dạng quốc tế duy nhất cho máy ảo. Format theo `RFC 4122`. Nếu thiếu trường `uuid` khi khởi tạo, mã này sẽ được tự động generate.
			```xml
			<uuid>d21b72cd-3568-4657-b5da-bc30eebdc6c7</uuid>
			```
		- Thẻ `<description>` : mô tả về máy ảo . Nếu không khai báo, mặc định sẽ là `None` 
			```xml
			<description>None</description>
			```
		- Thẻ `<memory>`: dung lượng RAM được cấp khi tạo máy ảo 
			```xml
			<memory unit='KiB'>524288</memory>
			```
		- Thẻ `<currentMemory>` : dung lượng RAM đang được sử dụng tại thời điểm ta xem file xml 
			```xml
			<currentMemory unit='KiB'>524288</currentMemory>
			```
		- Thẻ `<vcpu>` : 
			```xml
			<vcpu placement='static'>1</vcpu>
			```
			- Các thuộc tính của thẻ :
				- `cpuset`: danh sách các cpu vật lí mà máy ảo sử dụng
				- `placement` : vị trí của cpu, giá trị bao gồm `static` và `dynamic`, trong đó `static` là giá trị mặc định.
	- **Block `<os>`** : khai báo các thành phần của OS guest
		- Thẻ `<type>` : chứa thuộc tính `arch` là kiến trúc CPU máy ảo mà `machine` là kernel của máy ảo
			```xml
			<type arch='x86_64' machine='pc-i440fx-rhel7.0.0'>hvm</type>
			```
		- Thẻ `<boot>` : chứa thuộc tính `dev` chỉ ra thiết bị dùng để boot. Nó có thể là  `hd`, `cdrom` hoặc `network`. Nếu có nhiều thiết bị được khai báo, nó sẽ được sắp xếp làm thứ tự ưu tiên .
			```xml
			<boot dev='hd'/>
    		<boot dev='cdrom'/>
			```
		- Thẻ `<bootmenu>` : Chỉ ra có cho khởi động boot menu hay không. Thuộc tính `enable` có giá trị `yes` hoặc `no`. Thuộc tính `timeout`(tùy chọn) là thời gian đợi trước khi sử dụng chế độ mặc định.
			```xml
			<bootmenu enable='yes'/>
			```
