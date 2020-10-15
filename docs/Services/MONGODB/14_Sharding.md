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
- [Tham khảo thêm](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key-indexes)
### **2.4) Chunk**
### **2.5) Balancer and Even Chunk Distribution**
### **2.6) Advantages of Sharding**
### **2.7) Considerations Before Sharding**
### **2.8) Sharded and Non-Sharded Collections**
- Một database có thể kết hộ từ nhiều ***sharded*** và ***unsharded*** collection.
    - ***Sharded*** collection được chia và phân phối khắp các **shard** trong **cluster**.
    - ***Unsharded*** collection được lưu trữ trong **primary shard**. Mỗi database sẽ có một **primary shard** của riêng nó. 

<p align=center><img src=https://i.imgur.com/Mh9EsNC.png width=40%></p>

### **2.9) Connecting to a Sharded Cluster**
- Cần phải kết nối tới **mongos router** để tương tác với bất cứ collection nào trong **sharded cluster**, bao gồm cả ***sharded*** và ***unsharded*** collection. Client không nên kết nối vào 1 **shard** đơn lẻ để thực hiện thao tác read hoặc write .

<p align=center><img src=https://i.imgur.com/pzV3G98.png width=40%></p>
