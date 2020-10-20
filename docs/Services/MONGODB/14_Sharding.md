# Sharding
## **1) Giới thiệu**
- **Sharding** là một tiến trình lưu giữ các bản ghi dữ liệu qua nhiều server và nó là một phương pháp của **MongoDB** để đáp ứng yêu cầu về sự gia tăng dữ liệu. Khi kích cỡ của dữ liệu tăng lên, một server đơn lẻ không thể đủ để lưu trữ dữ liệu. **Sharding** giải quyết vấn đề này với việc mở rộng phạm vi theo bề ngang (*horizontal scaling*). Với **Sharding**, có thể bổ sung thêm nhiều server để hỗ trợ cho việc gia tăng dữ liệu và các yêu cầu của các hoạt động đọc và ghi.
- Lý do nên sử dụng **Sharding** :
    - Trong **Replication**, tất cả hoạt động ghi thực hiện ở node master (**primary**).
    - Các truy vấn nhạy cảm về độ trễ vẫn đẩy đến node master.
    - Một **Replica Set** đơn có giới hạn là `12` node.
    - Bộ nhớ không thể đủ lớn khi tập dữ liệu hoạt động là lớn.
    - Local Disk là không đủ lớn.
    - Việc mở rộng phạm vi theo chiều dọc (*vertical scaling*) là quá tốn kém
## **2) Các khái niệm chính trong sharding**
### **2.1) Sharding Cluster**
- Một **sharding cluster** trong **MongoDB** bao gồm các thành phần sau :
    - **Shards** : được sử dụng để lưu trữ dữ liệu. Cung cấp tính khả dụng cao (high availability) và dữ liệu có tính đồng nhất. Trong môi trường độc lập, mỗi **Shard** là một **replica set** riêng biệt .
    - **mongos** : hay còn được gọi là **query routers**, về cơ bản nó là mongo instance, cung cấp interface giữa client application và **sharded cluster**. Một **shared cluster** có thể chứa nhiều hơn một **mongos** để phân chia tải từ client. Một client sẽ gửi request đến 1 **mongos**. Nói chung 1 **shared cluster** sẽ có nhiều **mongos** . Từ bản `4.4` trở đi **mongos** có thể hỗ trợ **hedged reads** để làm tối thiểu độ trễ
    - **config servers** : lưu trữ metadata của và cấu hình cài đặt của các **cluster** . **Mongos** sử dụng các metadata này đế hướng các hoạt động đến các **shard** cụ thể .

<p align=center><img src=https://i.imgur.com/FGexzbT.png width=40%></p>

- **MongoDB** chia nhỏ dữ liệu ở mức **collection**, phân phối dữ liệu collection khắp các shard trong cluster .
### **2.2) Shard keys**
- **MongoDB** sử dụng **shard key** để phân phối các document của các collection trên các **shard**. **Shard key** bo gồn một trường hoặc nhiều trường trong document.
- Bắt đầu từ bản `4.4`, document trong **sharded collection** có thể thiếu trường **shard key**. Các trường **shard key** bị thiết sẽ được coi như có giá trị `NULL` kho phân phối document vào trong các **shard**. Trong bản `4.2` trở về trước, **shard key** chắc chắn tồn tại trong các document của các **sharded collection**
- [Tham khảo thêm](https://docs.mongodb.com/manual/sharding/#shard-keys)
### **2.3) Shard key Index**
- Để **shared** một **collection**, collection phải có **index** bắt đầu bằng **shard key**. Khi **sharding** một collection trống, **MongoDB** tạo **supporting index** nếu **collection** không có index được chỉ định cho **shard key**
- Việc lựa chọn **shard key** ảnh hưởng đến hiệu năng và khả năng mở rộng của **shared cluster**. Một cluster với phần cứng và hạ tầng tốt nhất có thể bị nghẽn cổ chai (**bottleneck**) do lựa chọn **shard key**. Việc lựa chọn **shard key** và các index có thể ảnh hưởng đến mô hình **sharding** mà cluster có thể sử dụng .
- [Tham khảo thêm về sharding key](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key-indexes)
- [Tham khảo thêm về cách lựa chọn shard key](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key-selection)
### **2.4) Chunk**
- **MongoDB** chia các sharded data ra thành các **chunk**. Mặc định **Chunk Size** là `64Mb` .
<p align=center><img src=https://i.imgur.com/W9QWADr.png width=40%></p>

### **2.5) Balancer and Even Chunk Distribution**
- Để phân phối đồng đều được các chunk trên tất cả các **shard** trong **cluster**, một **balancer** sẽ chạy ngầm để migrate các **chunk** trên các **shard** .
- **Chunk split** : là tiến trình chia cắt khi **Chunk** tăng đến giới hạn **(Chunk Size)** hoặc khi số lượng Document trong Chunk vượt quá tham số `Maximum Number of Documents Per Chunk to Migrate`. Dựa trên **Shard Key**, **MongoDB** sẽ tiến hành chia cắt thành nhiều **Chunk** nếu cần .
<p align=center><img src=https://i.imgur.com/ujEyer9.png width=30%></p>

- **Chunk Migration** : **MongoDB** chuyển đổi **Chunk** để phân bố lại chunk của Shard Collection. Việc này cũng như việc chúng ta Defragment lại ổ cứng trên Window. **MongoDB** hỗ trợ 2 phương pháp để làm được việc này:
    - **Manual:** Chỉ nên sử dụng trong một số trường hợp nhất định như phân tán dữ liệu sử dụng Bulk Insert
    - **Automatic:** Thông qua tiến trình **Balancer** tự động Migrate lại **chunk** khi có sự phân bố không đều giữa các **chunk** trên các **Shards**
- **Balancer** là một tiến trình chạy ngầm quản lý **chunk migration**. Nếu có sự chênh lệch số **số chunk** giữa shard lớn nhất và nhỏ nhất đạt đến **migration threshold**, **balancer** sẽ bắt đầu migrate **chunk** trên cluster để đảm bảo việc phân phối đều dữ liệu .
<p align=center><img src=https://i.imgur.com/DLwSWCM.png width=40%></p>

- [Tham khảo Data Partition with Chunks](https://docs.mongodb.com/manual/core/sharding-data-partitioning/)
- [Tham khảo về Balancer](https://docs.mongodb.com/manual/core/sharding-balancer-administration/#sharding-balancing)
### **2.6) Thuận lợi của Sharding**
#### **Read/Write**
- **MongoDB** phân phối tải đọc và ghi đều trên các **shard** trên các **sharded cluster**, cho phép mỗi **shard** sử lý một mảng của toàn bộ hoạt động . Cả tải địc và ghi đều có thể mở rộng theo chiều ngang trên cluster bằng việc thêm vào nhiều **shard** hơn .
#### **Khả năng lưu trữ**
- **Sharding** phân phối dữ liệu khắp các shard trong cluster, cho pháp các shard lưu trữ một mảng của toàn bộ dữ liệu trong cluster. Khi nhu cầu dữ liệu tăng, chỉ cần add thêm **shard** là sẽ tăng được khả năng lưu trữ của cluster .
#### **High Availability**
- Việc deploy **config server** và các **shard** là các **replica set** cung cấp thêm tính sẵn sàng của hệ thống .
- Thậm chí dù 1 hoặc nhiều **replica set** bị chết hoàng toàn, **sharded cluster** vẫn có thể tiếp tục đọc và ghi với phần dữ liệu còn lại. Thật vậy, trong khi các dữ liệu trên **shard** bị chết không thể truy cập được, các tác vụ đọc hoặc ghi được trỏ đến các **shard** đang bình thường vẫn thành công .
### **2.7) Những vấn đề cần cân nhắc trước khi thực hiện Sharding**
- Kiến trúc của một **sharding cluster** khá phức tạp yêu cầu một kế hoạch và triển khai cùng sự maitain một cách cẩn thận, kỹ lưỡng .
- Một khi một collection bị shard, **MongoDB** sẽ không có cách nào để unshard sharded collection đó .
- Cân nhắc kỹ lưỡng trong việc lựa chọn **shard key** là cần thiết để đảm bảo hiệu năng của cluster.
- Sharding có các quy tắc và giới hạn trong việc vận hành. [Tham khảo](https://docs.mongodb.com/manual/core/sharded-cluster-requirements/)
### **2.8) Sharded and Non-Sharded Collections**
- Một database có thể kết hộ từ nhiều ***sharded*** và ***unsharded*** collection.
    - ***Sharded*** collection được chia và phân phối khắp các **shard** trong **cluster**.
    - ***Unsharded*** collection được lưu trữ trong **primary shard**. Mỗi database sẽ có một **primary shard** của riêng nó. 

<p align=center><img src=https://i.imgur.com/Mh9EsNC.png width=40%></p>

### **2.9) Kết nối tới Sharded Cluster**
- Cần phải kết nối tới **mongos router** để tương tác với bất cứ collection nào trong **sharded cluster**, bao gồm cả ***sharded*** và ***unsharded*** collection. Client không nên kết nối vào 1 **shard** đơn lẻ để thực hiện thao tác read hoặc write .
<p align=center><img src=https://i.imgur.com/pzV3G98.png width=40%></p>

- Có thể connect tới **mongos** giống như cách kết nối tới `mongod`, sử dụng mongo shell hoặc MongoDB driver .
### **2.10) Phương pháp sharding**
- **MongoDB** hỗ trợ 2 phương pháp **sharding** để phân phối data trên các sharded cluster .
#### **2.10.1) Hashed Sharding**
- **Hashed Sharding** đòi hỏi việc tính toán hàm băm của giá trị trường **shard key**. Mỗi **chunk** sau đó sẽ được gán một range dựa trên giá trị **shard key** đã được hash .
<p align=center><img src=https://i.imgur.com/mN8Rs9x.png width=50%></p>

- Cách này giúp đảm bảo document phân tán được triệt để hơn
- [Tìm hiểu thêm về Hashed Sharding](https://docs.mongodb.com/manual/core/hashed-sharding/)
#### **2.10.2) Ranged sharding** 
- **Ranged sharding** đòi hỏi việc chia dữ liệu dựa trên giá trị của **shard key**. Mỗi **chunk** sẽ được gán vào một range dựa trên giá trị của **shard key** .
<p align=center><img src=https://i.imgur.com/pZXWZA1.png width=50%></p>

- **VD :** shardA chiến từ -10 đến 10. shardB chiếm từ 11 đến 31 chẳng hạn
- Một dãy các **shard key** có giá trị gần nhau gần như sẽ được sắp xếp trên một **chunk**. Điều này giúp cho các tác vụ tìm đúng mục tiêu vì một **mongos** có thể định tuyến tác vụ tới đúng **shard** chứa dữ liệu cần tìm.
- Hiệu suất của **ranged sharding** dựa trên **shard key** được chọn. Cân nhắc **shard key** không kỹ lưỡng có thể dẫn đến việc phân phối data không đồng đều, làm trái với mục tiêu của **sharding** hoặc gây ra hiện tượng nghẽn cổ chai.
- [Tham khảo thêm về Ranged Sharding](https://docs.mongodb.com/manual/core/ranged-sharding/)
### **2.11) Zones trong Sharded Cluster**
- **Zones** có thể giúp cải thiện việc định vị dữ liệu trên các **shard cluster** trên các **data center** khác nhau .
- Trên **shard cluster**, có thể tạo zone các sharded data dựa trên **shard key**. Có thể liên kết mỗi **zone** với 1 hoặc nhiều **shard** trong cluster. Mỗi **shard** cũng có thể kết nối tới nhiều **zone**. 
- Trong mỗi balanced cluster, **MongoDB** chỉ migrate các **chunk** trong một **zone** đến các **shard** liên kết đến **zone** đó .
- Mỗi **zone** bao gồm 1 hoặc nhiều range giá trị **shard key**. Mỗi range trong một **zone** luôn bao gồm cả biên trên và biên dưới của nó .
<p align=center><img src=https://i.imgur.com/XU7TGDt.png width=50%></p>

- Phải sử dụng các field có chứa **shard key** khi định nghĩa mới một range trong **zone**. Nếu sử dụng **compound shard key**, range phải chứa prefix của **shard key**
- [Tham khảo thêm về shard key trong zone](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding-shard-key)
- [Tham khảo thêm về zone](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding)
### **2.12) Collations in Sharding**
- [Tham khảo thêm](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection)
## **3) Deploy Sharded Cluster**
### **Mô hình**
<img src=https://i.imgur.com/yCKlNex.png>

### **Cài đặt MongoDB**
- **B1 :** Khai báo hostname cho các node :
    ```
    # hostnamectl set-hostname [mongodb_{n}]
    # bash
    ```
- **B2 :** Khai báo file `/etc/hosts` trên các node :
    - Trên `rs1` :
        ```
        # echo "127.0.0.1 localhost" > /etc/hosts
        # echo "10.5.11.101 mongodb_1" >> /etc/hosts
        # echo "10.5.11.102 mongodb_2" >> /etc/hosts
        # echo "10.5.11.103 mongodb_3" >> /etc/hosts
        ```
    - Trên `rs2` :
        ```
        # echo "127.0.0.1 localhost" > /etc/hosts
        # echo "10.5.11.104 mongodb_4" >> /etc/hosts
        # echo "10.5.11.105 mongodb_5" >> /etc/hosts
        # echo "10.5.11.106 mongodb_6" >> /etc/hosts
        ```
- **B3 :** Cài đặt **MongoDB `4.4`** trên tất cả các node. [Tham khảo](https://github.com/QuocCuong97/Linux/blob/master/docs/Services/MONGODB/02_Installation.md)