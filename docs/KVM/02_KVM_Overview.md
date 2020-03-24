# Tổng quan về KVM
## **1) Giới thiệu**
- **KVM** - **Kernel-based Virtual Machine** là giải pháp ảo hóa cho hệ thống Linux trên nền tảng phần cứng `x86` cho các module mở rộng hỗ trợ ảo hóa ( **Intel VT-x** hoặc **AMD-V** ) .
- **KVM** không thực sự là một **hypervisor** có chức năng giả lập phần cứng để chạy các máy ảo mà chỉ là một module của kernel linux hỗ trợ cơ chế mapping các chỉ dẫn trên CPU ảo (của guest VM) sang chỉ dẫn trên CPU vật lý (của máy chủ chứa VM) .
- **KVM** ban đầy được phát triển bởi **Qumranet** - một công ty nhỏ , sau đó được **RedHat** mua lại vào tháng `9` năm `2008` .
- **KVM** được sử dụng mặc định từ bản **RHEL** `5.4` và các phiên bản **RedHat Enterprise Virtualization** cho server .
- **Qumranet** phát hành mã của **KVM** cho cộng đồng mã nguồn mở .
- Từ bản `2.6.20` , **KVM** trở thành một phần trong kernel Linux .
## **2) Cấu trúc của KVM**
- Trong kiến trúc **KVM**, máy ảo sẽ là một tiến trình Linux, được lập lịch bởi chuẩn Linux Scheduler .
- Trong thực tế mỗi CPU ảo xuất hiện như là một tiến trình Linux => điều này cho phép **KVM** sử dụng tất cả các tính năng của Linux Kernel .
- Cấu trúc tổng quan :

    <img src=https://i.imgur.com/lV5Lviv.png>

- Linux có tất cả các cơ chế của một **VMM** cần thiết để vận hành (chạy) các máy ảo. Chính vì vậy các nhà phát triển không xây dựng lại mà chỉ thêm vào đó một vài thành phần để hỗ trợ ảo hóa. KVM được triển khai như một module hạt nhân có thể được nạp vào để mở rộng Linux bởi những khả năng này.
- Trong một môi trường linux thông thường mỗi process chạy hoặc sử dụng **user-mode** hoặc **kernel-mode**. **KVM** đưa ra một chế độ thứ 3, đó là **guest-mode**. Nó dựa trên CPU có khả năng ảo hóa với kiến trúc **Intel VT** hoặc **AMD SVM**, một process trong **guest-mode** bao gồm cả **kernel-mode** và **user-mode** .
- Cấu trúc tổng quan của **KVM** bao gồm 3 thành phần chính :
    - **KVM kernel module** :
        - Là một phần trong Linux kernel .
        - Cung cấp giao diện chung cho Intel VT-x và AMD-V
        - Chứ những mô phỏng cho các chỉ dẫn và CPU modes không được support bởi **Intel VT-x** và **AMD-V** .
    - **QEMU-KVM** : là chương trình dòng lệnh để tạo các máy ảo, thường được vận chuyển dưới dạng package "`kvm`" hoặc "`qemu-kvm`" . Có 3 chức năng chính :
        - Thiết lập **KVM** và các thiết bị ra vào (I/O)
        - Thực thi mã khách thông qua **KVM kernel module** .
        - Mô phỏng các thiết bị vào ra (I/O) và di chuyển các **guest** từ host này sang host khác .
    - **Libvirt Mangagement Stack** :
        - Cung cấp **API** để các tool như `virsh` có thể giao tiếp và quản lý các **VM** .
        - Cung cấp chế độ quản lý từ xa an toàn .