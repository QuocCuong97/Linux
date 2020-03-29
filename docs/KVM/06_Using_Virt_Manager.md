# Sử dụng `virt-manager` để quản lý VMs
> **Chú ý:** Các thao tác tương tự trên **CentOS** và **Ubuntu** có giao diện đồ họa
- Trong terminal chạy lệnh `virt-manager` hoặc chọn app "**Virtual Machine Manager**" trong menu app :

    <img src=https://i.imgur.com/hrugbbW.png>

- Giao diện của `virt-manager` :

    <img src=https://i.imgur.com/AHX0sAX.png>

## **1) Tạo VM mới**
- **B1 :** Trên thanh công cụ, chọn ***File*** -> ***New Virtual Machine*** :

    <img src=https://i.imgur.com/e8hztmo.png>

- **B2 :** Cửa sổ **Create a new virtual machine** mở ra, chọn ***Local install media (ISO image or CDROM)*** ( nếu cài từ file `.iso`) -> ***Forward*** :

    <p align=center><img src=https://i.imgur.com/kLXLdLQ.png width=70%></p>

- **B3 :** Tại tùy chọn ***Use ISO image*** , browse đến file `.iso` để cài đặt OS đã tải trước đó -> ***Forward*** :

    <p align=center><img src=https://i.imgur.com/dTQOCHg.png width=70%></p>

- **B4 :** Tại tùy chọn ***Memory (RAM)***, chọn dung lượng RAM muốn cấp cho máy ảo . Tại tùy chọn ***CPUs*** , chọn số **vCPUs** muốn cấp cho máy ảo -> ***Forward*** :

    <p align=center><img src=https://i.imgur.com/RfqFbYk.png width=70%></p>

- **B5 :** Tại tùy chọn ***Create a disk image for the virtual machine*** , chọn dung lượng disk muốn cấp cho máy ảo -> ***Forward*** :

    <p align=center><img src=https://i.imgur.com/FVhiDIC.png width=70%></p>

- **B6 :** Tại tùy chọn ***Name*** , nhập tên máy ảo . Tại tùy chọn ***Network selection***, chọn card mạng cho máy ảo -> ***Finish*** :

    <p align=center><img src=https://i.imgur.com/LePkvua.png width=70%></p>

- **B7 :** Duyệt lại các thông số của máy ảo lần cuối, sau đó chọn ***Begin Installation*** để tiến hành cài đặt máy ảo:

    <img src=https://i.imgur.com/n96gb0R.png>

- **B8 :** Tiến hành cài đặt như bình thường :

    <img src=https://i.imgur.com/eE2ovGh.png>

    <img src=https://i.imgur.com/RFWNsj3.png>

## **2) Snapshot** 
- **B1 :** Trên cửa sổ của máy ảo đang bật, chọn vào biểu tượng snapshot ở cuối thanh công cụ :

    <img src=https://i.imgur.com/buOVgQS.png>

- **B2 :** Chọn biểu tượng dấu "+" ở dưới góc trái màn hình để tạo snapshot :

    <img src=https://i.imgur.com/Gz06Ps4.png>

- **B3 :** Tại option ***Name*** , đặt tên cho bản snapshot -> ***Finish*** :

    <img src=https://i.imgur.com/40eVFV4.png>

    => Kết quả sau khi tạo :

    <img src=https://i.imgur.com/tTmBYan.png>

- **B4 :** Chọn biểu tượng ... để reverse bản snapshot :

    <img src=https://i.imgur.com/kG3bhv1.png>

- **B5 :** Chọn biểu tượng ... để xóa bản snapshot :

    <img src=https://i.imgur.com/BntUP7s.png>

## **3) Xem và sửa thông tin hardware của VM**
- **B1 :** Trên cửa sổ của máy ảo đang bật, chọn vào biểu tượng info trên thanh công cụ :

    <img src=https://i.imgur.com/gZUNCun.png>

- **B2 :** Cửa sổ thông tin phần cứng của máy ảo hiện ra, có thể tùy chỉnh sau đó chọn ***Apply*** .

    <img src=https://i.imgur.com/TxaxlIs.png>

    > Lưu ý : Phải tắt máy ảo trước khi sửa đổi thông tin