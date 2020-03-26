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
    - ### Thẻ `<domain>` là thành phần không thể thiếu, chứa thuộc tính `type` cho biết hypervisor đang sử dụng :
		```xml
		<domain type='kvm'>
		```
	- ### **Metadata của máy ảo** :
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
	- ### **Block `<os>`** : khai báo các thành phần của OS guest
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
	- ### **Block `<feature>`** : hypervisor cho phép 1 số option tự động bật /tắt
		```xml
		<features>
		  <acpi/>
		  <apic/>
		</features>
		```
	- ### **Block `<cpu>`** : mô tả các yêu câu của guest host về CPU
		```xml
		<cpu mode='host-model' check='partial'>
          <model fallback='allow'/>
      	</cpu>
		```
	- ### **Block `<clock>`** : thiết lập thời gian cho guest host
		```xml
		<clock offset='utc'/>
		```
	- ### **Block `<on....>`** : chỉ ra một số hành đông xảy ra sự kiến của guest os :
		```xml
		<on_poweroff>destroy</on_poweroff>
		<on_reboot>restart</on_reboot>
		<on_crash>restart</on_crash>
		```
		- Trong đó :
			- `<on_poweroff>` : hành động được thực hiện khi người dùng yêu cầu tắt máy.
			- `<on_reboot>` : hành động được thực hiện khi người dùng yêu cầu reset máy.
			- `<on_crash>` : hành động được thực hiện khi có sự cố. `<on_crash>` hỗ trợ 2 hành động:
				- `coredump-destroy` : domain bị lỗi sẽ được dump trước khi bị chấm dứt và giải phóng tài nguyên
				- `coredump-restart` : domain bị lỗi sẽ được dump trước khi được khởi động lại với cấu hình cũ
		- Những hành động được phép thực thi:
			- `destroy` : chấm dứt và giải phóng tài nguyên
			- `restart` : chấm dứt rồi khởi động lại giữ nguyên cấu hình
			- `preserve` : chấm dứt nhưng dữ liệu vẫn được lưu lại
			- `rename-restart` : khởi động lại với tên mới
			> `destroy` và `restart` được hỗ trợ trong cả `<on_poweroff>` và `<on_reboot>`. `preserve` dùng trong `<on_reboot>`, `rename-restart` dùng trong `<on_poweroff>`
	- ### **Block `<device>`** :
		- Thẻ `<emulator>` : đường dẫn tới thiết bị mô phỏng nhị phân 
			```xml
			<emulator>/usr/libexec/qemu-kvm</emulator>
			```
		- Block `<disk>` : phần cấu hình ổ đĩa với các thuộc tính 
			- `type` : có thể là `file`, `block`, `dir`, `network` hoặc `volumes`
			- `device` : cách ổ đĩa tiếp xúc với hệ điều hành . Các giá trị có thể chọn là "`disk`", "`cdrom`", "`lun`". Giá trị mặc định là "`disk`" .
			- `snapshot` : chọn chế độ mặc định của ổ đĩa khi snapshot. Các giá trị ở đây là "`internal`", "`external`" và "`no`"
			```xml
			<disk type='file' device='disk'>
			```
			- Thẻ `<driver>` : gồm thuộc tính `name` là tên trình điều khiển hỗ trợ: mặc định là "`qemu`" ; thuộc tính `type` : các giá trị có thể chọn : "`raw`", "`bochs`", "`qcow2`", và "`qed`"
				```xml
				<driver name='qemu' type='raw'/>
				```
			- Thẻ `<source>` : gồm thuộc tính `file` là đường dẫn tới ổ đĩa và thuộc tính `dir` là đường dẫn rới thư mục chứa ổ đĩa
				```xml
				<source file='/var/lib/libvirt/fileiso/CentOS-7-x86_64-Minimal-1908.iso'/>
				```
			- Thẻ `<target>` : gồm thuộc tính `dev` là tên loại ổ đĩa (`vda`, `hda`,...) và thuộc tính `bus` là loại controller của ổ đĩa (`virtio`, `xen`, `ide`, `scsi`, `sata`, `usb`, `sata`, `sd`)
				```xml
				<target dev='hda' bus='ide'/>
				```
			- Thẻ `<address>` : gồm thuộc tính `type` là loại controller(`pci` hoặc `drive`). Trong đó với "`drive`", các giá trị "`controller`", "`bus`", "`target`" và "`unit`" sẽ có giá trị mặc định bằng `0`
				```xml
				<address type='drive' controller='0' bus='0' target='0' unit='0'/>
				```
		- Block `<controller>` : 
			- Tùy thuộc vào cấu trúc của máy ảo mà nó có thể có các thiết bị ảo đi kèm, mỗi cái lại đi theo một bộ điều khiển. Thường thì libvirt sẽ tự động chỉ ra mà không cần khai báo qua file xml.
			- Mỗi bộ điều khiển có một tham số bắt buộc là `type` và `index`, các giá trị có thể chọn của type là: '`ide`', '`fdc`', '`scsi`', '`sata`', '`usb`', '`ccid`', '`virtio-serial`' hoặc '`pci`'. Trong khi đó `index` sẽ chỉ ra thứ tự ưu tiên.
			```xml
			<controller type='usb' index='0' model='piix3-uhci'>
			  <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x2'/>
			</controller>
			<controller type='pci' index='0' model='pci-root'/>
			<controller type='ide' index='0'>
			  <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x1'/>
			</controller>
			```
		- Block `<interface>` : 
			- Có một vài kiểu set up network ví dụ như Virtual network (`type` = `network`), Bridge to LAN (`type` = `bridge`), Userspace SLIRP stack (`type`=`user`)
			- Thẻ `<mac>` : với thuộc tính là `address` để khai báo địa chỉ mac
			- Thẻ `<source>` : tên loại card mạng
			- Thẻ `<model>` : tham số bắt buộc là "type", các giá trị thường được sử dụng trong KVM: "`ne2k_isa`, `i82551`, `i82557b`, `i82559er`, `ne2k_pci`, `pcnet`, `rtl8139`, `e1000`, `virtio`"
			```xml
			<interface type='network'>
			  <mac address='52:54:00:cb:5e:9a'/>
			  <source network='default'/>
			  <model type='rtl8139'/>
			  <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
			</interface>
			```
		- Block `<serial>`, `<console>` :
			```xml
			<serial type='pty'>
			  <target type='isa-serial' port='0'>
				<model name='isa-serial'/>
			  </target>
			</serial>
			<console type='pty'>
			  <target type='serial' port='0'/>
			</console>
			```
		- Block `<input>` : gồm các thẻ `<input>` với các thuộc tính 
			- `type` : các giá trị có thể chọn là "`mouse`", "`tablet`", "`keyboard`", "`passthrough`"
			- `bus` : xác định chính xác kiểu kết nối ("`virtio`", "`usb`", "`ps2`")
			```xml
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
			```
		- Block `<graphic>` : khai báo phần cứng đồ họa . Thuộc tính bắc buộc là `type`, các giá trị có thể chọn : "`sdl`", "`vnc`", "`spice`", "`rdp`" và "`desktop`". Đối với mỗi loại sẽ có thêm những tham số được thêm vào.
			```xml
			<graphics type='vnc' port='-1' autoport='yes' listen='0.0.0.0'>
			  <listen type='address' address='0.0.0.0'/>
			</graphics>
			```
		- Block `<video>` : 
			- Thẻ `<model>` : tham số bắt buộc là "`type`", các giá trị có thể lựa chọn là "`vga`", "`cirrus`", "`vmvga`", "`xen`", "`vbox`", "`qxl`", "`virtio`" và "`gop`", tùy thuộc vào hypervisor. Thuộc tính `head` chỉ ra số lượng màn hình . Thuộc tính `ram` và `vram` chỉ ra kích thước của primary và secondary bar
			```xml
			<video>
			  <model type='vga' vram='16384' heads='1' primary='yes'/>
			  <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x0'/>
			</video>
			```
		- Thẻ `<memballoon>` : được thêm tự động, mặc định với KVM, model sẽ là `virtio` 
			```xml
			<memballoon model='virtio'>
			  <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0'/>
			</memballoon>
[Tham khảo thêm](https://libvirt.org/formatdomain.html#elementsDevices)
## **2) Cách tạo máy ảo bằng 1 file `.xml`**
- **B1 :** Chuẩn bị file `.xml` :
	```
	# vi /etc/libvirt/qemu/CentOS7-01.xml
	```
	Thêm nội dung sau :
	```xml
	<domain type='kvm'>
	  <name>CentOS7-01</name>
	  <uuid>a96ff9c2-6f0b-11ea-a76b-000c29764151</uuid>
	  <memory>1048576</memory>
	  <currentMemory>1048576</currentMemory>
	  <vcpu>1</vcpu>
	  <os>
		<type>hvm</type>
		<boot dev='cdrom'/>
	  </os>
	  <features>
		<acpi/>
	  </features>
	  <clock offset='utc'/>
	  <on_poweroff>destroy</on_poweroff>
	  <on_reboot>restart</on_reboot>
	  <on_crash>destroy</on_crash>
	  <devices>
		<emulator>/usr/libexec/qemu-kvm</emulator>
		<disk type="file" device="disk">
		  <driver name="qemu" type="qcow2"/>
		  <source file="/var/lib/libvirt/images/centos7-01.qcow2"/>
		  <target dev="vda" bus="virtio"/>
		  <address type="pci" domain="0x0000" bus="0x00" slot="0x04" function="0x0"/>
		</disk>
		<disk type="file" device="cdrom">
		  <driver name="qemu" type="raw"/>
		  <source file="/var/lib/libvirt/fileiso/CentOS-7-x86_64-Minimal-1908.iso"/>
		  <target dev="hdc" bus="ide"/>
		  <readonly/>
		  <address type="drive" controller="0" bus="1" target="0" unit="0"/>
		</disk>
		<interface type='network'>
		  <source network='default'/>
		  <model type='rtl8139'/>
		  <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
		</interface>
		<controller type="ide" index="0">
		  <address type="pci" domain="0x0000" bus="0x00" slot="0x01" function="0x1"/>
		</controller>
		<input type='mouse' bus='ps2'/>
		<graphics type='vnc' port='-1' autoport="yes" listen='0.0.0.0'/>
		<console type='pty'>
		  <target port='0'/>
		</console>
	  </devices>
	</domain>
	```
	- Máy ảo này sẽ có thông số :
		- Tên máy : CentOS7-01
		- RAM : `1GB`
		- vCPU : `1`
		- Đường dẫn tới disk : `/var/lib/libvirt/images/centos7-01.qcow2`
		- Máy ảo được boot từ CDROM : `/var/lib/libvirt/fileiso/CentOS-7-x86_64-Minimal-1908.iso`
		- Sử dụng card NAT default
	- Phần `uuid` có thể sử dụng lệnh để generate :
		```
		# yum install -y uuid
		# uuid
		a96ff9c2-6f0b-11ea-a76b-000c29764151
		```
	> Có thể tạo file `.xml` bằng cách dump 1 file tương tự từ máy ảo khác bằng lệnh `virsh dumpxml vm01 > vm02.xml`
- **B2 :** Tạo ổ đĩa vừa khai báo ở trên :
	```
	# qemu-img create -f qcow2 /var/lib/libvirt/images/centos7-01.qcow2 10G
	```
- **B3 :** Khởi tạo máy ảo :
	```
	# virsh create CentOS7-01.xml
	```
- **B4 :** Kiểm tra máy ảo vừa tạo :
	```
	# virsh list
	 Id    Name                           State
	----------------------------------------------------
	19    CentOS7-02                     running
	22    CentOS7-01                     running
	```