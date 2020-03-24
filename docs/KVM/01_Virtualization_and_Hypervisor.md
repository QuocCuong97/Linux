# Tổng quan về Virtualization và Hypervisor
## **1) Virtualization**
- **Virtualization** là một công nghệ cho phép ta cài đặt nhiều hệ điều hành ( **OS** ) khác nhau trên cùng một phần cứng . Chúng hoàn toàn riêng rẽ và độc lập với nhau .
- **Virtualization** ẩn các thông số của tài nguyên phần cứng khỏi các user, các ứng dụng của các OS hoặc người dùng quản lý từng OS . Điều này tương tự như việc biến một tài nguyên vật lý ( như server, hệ điều hành, một ứng dụng hoặc một thiết bị storage ) thành nhiều tài nguyên ảo .
- Các loại **virtualization** :
    - Server Virtualization
    - Desktop Virtualization
    - Operating System Virtualization
    - Network functions Virtualization
    - Storage Virtualization
### **1.1) Server Virtualization**
- Đây là dạng ảo hóa hạ tầng server thành nhiều server ảo cho nhiều mục đích khác nhau .

    <img src=https://i.imgur.com/wQaVjCR.png>

### **1.2) Desktop Virtualization**
- Tương tự với **server virtualization**, nhưng lần này là dựa trên giao diện người dùng để ảo hóa các Desktop Client .

    <img src=https://i.imgur.com/Y5VIIa6.png>

### **1.3) Operating System Virtualization**
- Dạng ảo hóa này sẽ cô lập các ứng dụng khỏi hệ điều hành và khỏi các ứng dụng khác , nhằm mục đích tăng khả năng tương thích và khả năng quản lý .
- Một ví dụ điển hình của dạng này là Docker .

    <img src=https://i.imgur.com/vZew9dm.png>

### **1.4) Network functions Virtualization**
- Đây là một phần của cơ sở hạ tầng ảo hóa, đặc biệt được sử dụng khi ảo hóa Server . Giúp ta tạo nên nhiều Switch ảo, các VLan, các mạng NAT, ...

    <img src=https://i.imgur.com/4gUCSRk.png>

### **1.5) Storage Virtualization**
- Dạng ảo hóa này được sử dụng rộng rãi trong các DataCenter, khi có một hệ thống lưu trữ lớn và muốn phân chia, gán chúng cho các máy chủ vật lý hoặc máy chủ ảo khác nhau . Việc gán này được thực hiện qua kết nối mạng .
- Hệ thống lưu trữ tổng được gọi là **SAN - Storage Area Network** .

    <img src=https://i.imgur.com/Y9ZryzR.png>

## **2) Hypervisors**
- Một **hypervisor** là một dạng phần mềm cho phép hệ điều hành giao tiếp với phần cứng .
- Chúng cũng được gọi là **Virtual Machine Monitor (VMM)** . 
- **Hypervisor** tạo nên một nền tảng ảo hóa ( ***virtual platform*** ) trên máy chủ , và dựa trên đó các máy ảo hoạt động và được quản lý .
- **Hypervisor** chia thành 2 loại :
    - **Native of Bare Metal Hypervisor**
    - **Hosted Hypervisor**
### **2.1) Native of Bare Metal Hypervisor**
- **Native Hypervisors** là các hệ phần mềm chạy trực tiếp trên phần cứng của máy chủ để kiểm soát và monitor các máy ảo .
- Các ví dụ điển hình của kiến trúc ảo hóa này là **Oracle VM**, **Microsoft Hyper-V**, **VMWare ESX** và **Xen** .

    <img src=https://i.imgur.com/SYbIaTU.png>

### **2.2) Hosted Hypervisor**
- **Hosted Hypervisor** được thiết kế để có thể chạy được trên các hệ điều hành truyền thống .
- Các hệ điều hành của máy ảo trở thành phần mềm cấp 3 so với phần cứng .
- Các ví dụ điển hình của kiến trúc ảo hóa này là **Oracle VM VirtualBox**, **VMware Workstation**, **KVM**, **QEMU**, **Parallels** .

    <img src=https://i.imgur.com/3RjtRgB.png>

## **3) Local Virtualization và Cloud**
- **Virtualization** là một trong các công nghệ nền tảng cho **Cloud-Computing** . Tuy nhiên, **virtualization** không phải là **Cloud-Computing** . **Cloud-Computing** là một dịch vụ mà các nhà cung cấp đưa đến người dùng dựa trên các mức giá khác nhau .
- Trong mạng doanh nghiệp, **virtualization** và **cloud-computing** thường được sử dụng cùng nhau để tạo nên hạ tầng **public cloud** hay **private cloud** .
- Trong các nhu cầu nhỏ hơn, mỗi công nghệ sẽ được triển khai riêng biệt để đạt được những mục đích khác nhau . Theo nhiều cách khác nhau , **virtualization** và **cloud-computing** có thể giúp tiết kiệm các thiết bị đến mức tối thiểu, cũng như sử dụng chúng hiệu quả nhất .
- Các doanh nghiệp hoặc người dùng các nhân thường sử dụng **cloud-computing** bằng cách thuê các dịch vụ **cloud-based** . Các nhà cung cấp dịch vụ **Cloud-Computing** lớn nhất hiện nay là **Microsoft (Azure)** và **Amazon (AWS - Amazon Web Services)**

    <img src=https://i.imgur.com/hJn1t6D.png>

## **4) Hardware Virtualization**
### **4.1) Virtual CPU**
- Khi cài đặt một **hypervisor** , mỗi **CPU** vật lý sẽ được tách ra thành các **virtual CPU** . Chúng sẽ chia phần CPU khả dụng ra thành các core ảo và cho phép các **VMs** được sử dụng chung dựa trên core vật lý . 
- Nói chung, **hypervisor** sẽ chỉ định khối lượng công việc phải làm cho mỗi **vCPU** . Nếu máy chủ cần nhiều **CPU** hơn, thì tốt nhất nên triển khai ít **VMs** hơn và trên từng **vCPU** riêng biệt
- Cách tính số lượng **vCPU** :
    - Giả dụ ta có 1 server với 2 **CPU** , mỗi **CPU** có 4 core vật lý . Tổng cộng ta có `2 * 4 = 8` cores vật lý .
    - Dựa trên một vài phép tính toán mà **hypervisor** cung cấp cho mỗi core vật lý , ta có thể có được `5-10` **vCPUs**
    - Tổng cộng ta có từ `40-80` **vCPUs** , có nghĩa là có thể gán tối đã `80` **vCPUs** cho một máy ảo .

        <img src=https://i.imgur.com/g764rsF.png>

### **4.2) Virtual Memory**
- Chính là **RAM** của máy ảo . Phần cài đặt tài nguyên **RAM** cho máy ảo sẽ quyết định phần **RAM** mà máy chủ sẽ cấp cho các máy ảo . Lượng **RAM** ảo sẽ quyết định có bao nhiêu bộ nhớ khả dụng cho các ứng dụng chạy trên máy ảo .

    <img src=https://i.imgur.com/EZ43xAB.png>

### **4.3) Virtual Storage**
- **Storage virtaulization** là 1 nhóm các vùng lưu trữ vật lý ( **Data cluster** ) từ nhiều thiết bị storage kết nối mạng và gộp thành 1 thiết bị lưu trữ được quản lý từ trung tâm . Không thể gán thêm dung lượng lưu trữ cho máy ảo khi **data cluster** không có đủ.
- Các định dạng phổ biến của phần dung lượng gán cho máy ảo là `.vdi`, `vhdx`, `vmdk` và `hdd` .

    <img src=https://i.imgur.com/VqexQZ8.png>

### **4.4) Virtual Networking**
- Các máy ảo trên cùng 1 host trao đổi dữ liệu với nhau dựa trên **vNICs** , kết nối trực tiếp đến các **vSwitch** được tạo ra bởi **hypervisor** .
- Các **vSwitch** giao tiếp với **pNIC** ( card mạng vật lý của host ) giúp các **vNICs** trao đổi dữ liệu ra các mạng bên ngoài, hay Internet ...

    <img src=https://i.imgur.com/r5ibIMe.png>


