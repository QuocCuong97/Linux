# Các chế độ card mạng trong KVM
## **1) NAT**
- Với mô hình này **KVM** thực hiện ánh xạ một dải địa chỉ để cung cấp cho máy ảo. Dải địa chỉ ta có thể chọn tùy ý. 
- Với mô hình này máy ảo của ta có thể giao tiếp với internet. Nhưng có một chú rằng từ VM của ta có thể ping được ra ngoài internet nhưng máy bên ngoài sẽ không thấy được VM của ta chính vì vậy ta không thể ping từ bên ngoài đến VM sử dụng mô hình mạng NAT.

    <p align=center><img src=https://i.imgur.com/XCsaf54.png width=80%></p>

### **Cách cài đặt NAT sử dụng `virt-manager`** :
- Bình thường khi cài KVM nó sẽ cung cấp cho ta một mạng ảo NAT mang tên `default` mạng này thường mang dải địa chỉ IP `192.168.122.x`. Ta có thể add thêm một mạng ảo cũng với mô hình NAT khác. 
- **B1 :** Truy cập `virt-manager` :
    ```
    # virt-manager
    ```
    <p align=center><img src=https://i.imgur.com/xnwzKLr.png width=80%></p>
- **B2 :** Chọn tab **Edit** -> ***Edit connection 
    <p align=center><img src=https://i.imgur.com/1z8F1TY.png width=80%></p>

- **B3 :** Chọn tab **Virtual Networks**, sau đó chọn dấu "`+`" ở góc trái màn hình để thêm card mạng mới :

    <p align=center><img src=https://i.imgur.com/Zy14NBn.png width=80%></p>

- **B4 :** Nhập tên dải mạng muốn thêm sau đó chọn ***Forward*** :

    <p align=center><img src=https://i.imgur.com/RzqAGlG.png width=60%></p>

- **B5 :** Chọn dải IP cấp phát cho Client, sau đó chọn ***Forward*** :

    <p align=center><img src=https://i.imgur.com/HVWvi5w.png width=60%></p>

- **B6 :** Bỏ qua IPv6 ở bước này, chọn ***Forward*** :

    <p align=center><img src=https://i.imgur.com/ZcRctzB.png width=60%></p>

- **B7 :** Chọn mode **NAT** -> ***Finish*** :

    <p align=center><img src=https://i.imgur.com/neMpNyy.png width=60%></p>

    => Kết quả sau khi tạo xong :

    <p align=center><img src=https://i.imgur.com/7TsC7bm.png width=80%></p>

- **B8 :** Đối với máy ảo , chọn vào phần thay đổi thông tin -> chọn tab **NIC** -> Thay đổi sang network mới tạo -> ***Apply*** :

    <p align=center><img src=https://i.imgur.com/XAEr8F9.png width=80%></p>

- **B9 :** Kiểm tra :
    - Máy ảo nhận đúng địa chỉ trong dải IP được cấp :
        ```
        # ip a
        ```
        <img src=https://i.imgur.com/ggEKLsK.png>

    - Máy ảo ping được ra Internet :
        ```
        # ping 8.8.8.8
        ```
        <img src=https://i.imgur.com/w6Txzu2.png>

## **2) Host-only (isolated mode)**
- Mô hình mạng kiểu Host-only cũng cho phép ta cấp phát địa chỉ tùy ý giống với mô hình NAT. Nhưng ở đây máy ảo không thể nói chuyện với máy tính bên ngoài. Nó chỉ có thể trao đổi với các máy trong cùng mạng bên trong server vật lý và trao đổi với đươc máy chủ vật lý .

    <p align=center><img src="https://i.imgur.com/ArQPUOe.png" width=80%></p>

### **Cách cài đặt Host-only sử dụng `virt-manager`** :
- **B1**, **B2**, **B3**, **B4** làm tương tự như với NAT
- **B5 :** Chọn dải IP cấp phát cho Client, sau đó chọn ***Forward*** :

    <p align=center><img src=https://i.imgur.com/5BDrLdx.png width=60%></p>

- **B6 :** Bỏ qua IPv6 ở bước này, chọn ***Forward*** :

    <p align=center><img src=https://i.imgur.com/ZcRctzB.png width=60%></p>

- **B7 :** Chọn ***Isolated virtual network*** -> ***Finish*** :

    <p align=center><img src=https://i.imgur.com/6nY4lrQ.png width=60%></p>

    => Kết quả sau khi tạo xong :

    <p align=center><img src=https://i.imgur.com/lzU9UrW.png width=80%></p>

- **B8 :** Đối với máy ảo , chọn vào phần thay đổi thông tin -> chọn tab **NIC** -> Thay đổi sang network mới tạo -> ***Apply*** :

    <p align=center><img src=https://i.imgur.com/ZJG5SKb.png width=80%></p>

- **B9 :** Kiểm tra :
    - Máy ảo nhận đúng địa chỉ trong dải IP được cấp :
        ```
        # ip a
        ```
        <img src=https://i.imgur.com/NyGq2C5.png>

    - Máy ảo không ping được ra Internet
        ```
        # ping 8.8.8.8
        ```
        <img src=https://i.imgur.com/TdwLkCZ.png>

## **3) Bridge**
- Đối với chế độ **bridge**, KVM sử dụng công nghệ **Linux Bridge** .
- **Linux Bridge** là một phần mềm được tích hợp trong nhân Linux để giải quyết vấn đề ảo hóa phần Network trong các máy vật lý .
- Về mặt logic, **Linux Bridge** tạo ra một con switch ảo để các VM kết nối vào và có thể nói chuyện với nhau cũng như sử dụng để ra ngoài mạng .

    <p align=center><img src=https://i.imgur.com/5xNgaoj.png width=80%></p>

- Các lệnh làm việc với **bridge** :
    - Tạo **bridge** :
        ```
        # brctl addbr bridge_name
        ```
    - Gán card cho **bridge** :
        ```
        # brctl addif bridge_name card_name
        ```
    - Kiểm tra lại hoạt động của **bridge** :
        ```
        # brctl show
        ```
    - Ngắt kết nối card khỏi **bridge** :
        ```
        # brctl delif bridge_name card_name
        ```
### **VD về cách cài đặt Bridge** :
- **B1 :** Trên máy KVM, tạo **bridge** :
    ```
    # brctl addbr bridge1
    ```
- **B2 :** Gán card cho **bridge** :
    ```
    # brctl addif bridge1 ens33
    ```
- **B3 :** Kiểm tra lại **bridge** :
    ```
    # brctl show
    ```
