# Replication
## **1) Khái niệm Replication**
- **Replication** là quá trình đồng bộ dữ liệu giữa nhiều server.
- **Replication** cung cấp tính dự phòng và tăng tính sẵn sàng cho dữ liệu bằng việc tạo ra nhiều bản copy của dữ liệu trên các database server khác nhau . **Replication** bảo vệ database khỏi bị mất trên 1 server riêng lẻ. **Replication** cũng cho phép khôi phục dữ liệu từ việc phần cứng bị hỏng hay service bị gián đoạn . Với các dữ liệu được copy thêm, có thể chỉ định cho các mục đích như khôi phục, báo cáo hay backup .
- Lí do cần **replication** :
    - Để dữ data an toàn
    - Tăng tính sẵn sàng dữ liệu (`24/7`)
    - Khôi phục được từ sự cố
    - Không có downtime khi bảo trì (như backup, index rebuild, nén dữ liệu)
    - Read scaling (có nhiều bản copy hơn để đọc)
    - **Replica set** là hoàn toàn trong suốt với các ứng dụng
## **2) Cách làm việc của replication trong MongoDB**
- Một **replica set** trong **MongoDB** là một nhóm các instance chạy **mongod** duy trì cùng một bộ dữ liệu. 
- Các **replica set** cung cấp tính dự phòng và tính sẵn sàng cao và là cơ sở để triển khai nhập xuất dữ liệu khi cần thiết
### **2.1) Mô hình replication 3 node**
- Một **replica set** chứa một số các ***data-bearing node*** và có thể có thêm 1 ***arbiter node***. Trong các ***data-bearing node***, một và chỉ một member sẽ là **primary node**, các node còn lại sẽ là **secondary node** .
- **Primary node** sẽ nhận các thao tác write. Một **replica set** chỉ có thể có 1 primary có khả năng write với cài đặt `{ w: "majority" }`. Các primary record chỉ thay đổi trên các data set của chính nó trong operation log, oplog,...

    <p align=center><img src=https://i.imgur.com/In34Te3.png width=40%></p>

- Các **Secondary node** sẽ replicate oplog của **primary** và apply quá trình đó vào các dataset do đó các data set của các **secondary** sẽ ánh xạ data set của **primary**. Nếu **primary** không khả dụng, một **secondary** xứng đáng sẽ tổ chức bầu chọn và bầu chọn chính nó làm **primary** mới ,

    <p align=center><img src=https://i.imgur.com/MoRiAmK.png width=40%></p>

- Trong một vài trường hợp (ví dụ như có 1 **primary** và 1 **secondary** nhưng không có thêm chi phí cho một **secondary** nữa), có thể chọn cách add thêm moojtn instance chạy `mongod` vào **replica set** như một **arbiter**. Một **arbiter** tham gia vào bình vầu nhưng sẽ không giữ data (hay không cung cấp cơ chế dự phòng dữ liệu)

    <p align=center><img src=https://i.imgur.com/V9i3jBA.png width=40%></p>

### **2.2) Cơ chế bình bầu giữa các node**
- **Replica set** sử dụng việc bình bầu để quyết định member nào sẽ là primary. **Replica set** thực hiện bình bầu primary khi xảy ra các event sau :
    - Thêm một node mới vào **replica set**
    - Khởi tạo một **replica set**
    - Thực hiện maintainance **replica set** bằng cách sử dụng phương thức `rs.stepDown()` hoặc `rs.reconfig()`, và các **secondary** sẽ mất kết nối tới **primary** lâu hơn thời gian timeout được cấu hình (mặc định `10s`)
- **Replica set** không thể thực hiện write dữ liệu cho tới khi việc bình bầu thành công. **Replica set** chỉ thực hiện được các câu truy vấn khi các **secondary** cũng có khả năng thực hiện được .
- Cơ chế bình bầu này cũng được gọi là cơ chế **Automatic Failover** :

    <p align=center><img src=https://i.imgur.com/W06mjKc.png width=40%></p>

#### **Các yếu tố ảnh hưởng đến việc bình bầu **primary****
- Giao thức **Replication Election Protocol** :
    - Từ version `4.0`, **MongoDB** chỉ hỗ trợ **replica set protocol version 1 `(pv1)`**
    - Tham khảo : [Replica Set Protocol Version](https://docs.mongodb.com/manual/reference/replica-set-protocol-versions/)
- **Heartbeat** :
    - Các member của **replica set** gửi các heartbeat (ping) tới nhau mỗi `2s`. Nếu heartbeat không phản hồi trong `10s`, các member khác sẽ đánh dấu member quá hạn này là *không thể truy cập được (inaccessible)*
- **Member Priority** :
    - Sau khi **replica set** chọn ra được 1 **primary**, thuật toán bầu chọn sẽ chọn những **secondary** có priority lớn nhất ở cuộc bầu chọn. **Member priority** ảnh hưởng đến cả thời gian và kết quả của cuộc bầu chọn. Các **secondary** có **priority** cao hơn sẽ tham gia bầu chọn trước, và có cơ hội thắng cao hơn. Tuy nhiên, cũng có trường hợp **priority** thấp hơn được chọn. Các member sẽ tiếp tục bầu cử cho đến khi chọn được thành viên tốt nhất .
    - Member có **priority** là `0` sẽ không thể trở thành **primary** và cũng không thể tham gia bầu cử
    - Tham khảo : [Priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/)
- **Mirrored Reads** :
    - Bắt đầu từ phiên bản `4.4`, **MongoDB** cung cấp cơ chế **mirror reads** để trước vào bộ nhớ cache của các **secondary** với dữ liệu được truy cập gần đây nhất . Với **mirror reads**, **primary** có thể ánh xạ tập hợp các thao tác mà nó nhận được đến và gửi chúng đến các **secondary**. Việc ánh xạ cache như vậy cũng giúp hiệu suất được phục hồi nhanh hơn sau khi việc bầu chọn diễn ra .
    - Tham khảo : [Mirror Reads](https://docs.mongodb.com/manual/replication/#mirrored-reads)
- **Mất Data Center** :
    - Với các **replica set** được phân tán, việc mất kết nối 1 data center cũng có thể ảnh hưởng đến khả năng của các member còn lại trong các data center khác khi bầu chọn **primary** .
    - Nếu có thể, hãy phân phối các member của **replica set** trên nhiều data center để tối đa hóa khả năng ngay cả khi mất một data center, các member khác cũng có thể bầu chọn lại **primary** .
    - Tham khảo : [Phân tán replica set trên 1 hoặc nhiều Datacenter](https://docs.mongodb.com/manual/core/replica-set-architecture-geographically-distributed/)
- **Network partition** :
    - Việc chia mạng có thể tách một **primary** vào phân vùng mạng mà ít thấy được các node khác . Khi **primary** phát hiện ra nó chỉ có thể giao tiếp với một số ít các node trong **replica set**, nó sẽ tự động bước xuống và trở thành **secondary** . Một member khác, có thể giao tiếp với nhiều node nhất trong **replica set** sẽ độc lập đứng lên tổ chức bầu chọn lại để trở thành **primary** .
#### **Các member được tham gia quá trình bầu chọn**
- Phần cấu hình `members[n].votes` của member trong **replica set**  và member state sẽ quyết định xem member có được ở trong cuộc bầu chọn không .
- Tất cả các member có `members[n].votes` sẽ được tham gia bầu chọn. Để loại trừ một member không cho tham gia bầu chọn, đổi giá trị này thành `0` .
    - Các member không được vote (`votes = 0`) phải có priority là `0`
    - Member có priority > `0` không thể đặt `votes = 0`
- Chỉ có các member được quyền vote ở đang trong các trạng thái (state) sau thì mới được tham gia vote :
    - `PRIMARY`
    - `SECONDARY`
    - `STARTUP2`
    - `RECOVERING`
    - `ARBITER`
    - `ROLLBACK`
#### **Các member không được tham gia bầu chọn**
- Mặc dù các member không có quyền sẽ không được bầu chọn, các member này vẫn giữ bản copy dữ liệu của **replica set** và có thể chấp nhận thao tác đọc từ các ứng dụng của client .
- Bởi vì **replica set** có tới `50` member, nhưng chỉ có `7` member được tham gia vote, non-voting member sẽ cho phép **replica set** có nhiều hơn 7 member .
- **VD :** Có `9` member trong **replica set** thì sẽ có `7` member được vote và 2 member không được vote :

    <p align=center><img src=https://i.imgur.com/3G1TFzr.png width=40%></p>

    - Non-vote member sẽ có cả `votes=0` và `priority=0` như sau :
        ```json
        {
            "_id" : <num>,
            "host" : <hostname:port>,
            "arbiterOnly" : false,
            "buildIndexes" : true,
            "hidden" : false,
            "priority" : 0,
            "tags" : {

            },
            "slaveDelay" : NumberLong(0),
            "votes" : 0
        }
        ```
## **3) Các kiến trúc deploy Replica set**
### **3.1) Kiến trúc 3 member**
- Số lượng member tối thiểu của **replica set** để thấy được lợi ích của nó là `3` member. Một **replica set** với `3` member có thể là cả `3` member đều mang dữ liệu (**Primary-Secondary-Secondary** - nên dùng) hoặc trường hợp khác cần tiết kiệm chi phí hơn khi thêm member mang dữ liệu thứ 3, đó là 2 member mang dữ liệu và 1 **arbiter** (**Primary-Secondary-Arbiter**)
#### **3.1.1) Kiến trúc Primary-Secondary-Secondary (P-S-S)**
- Một **replica set** với 3 members lưu trữ data có :
    - 1 **primary**
    - 2 **secondary**. Các **secondary** đều có thể trở thành **primary** sau khi bình chọn .

        <p align=center><img src=https://i.imgur.com/MqYQLGz.png width=40%></p>

- Cách deploy này cung cấp 2 bản copy hoàn chỉnh của data set tại mọi thời điểm trong **primary**. **Replica set** cung cấp khả năng chịu lỗi và tính sẵn sàng. Nếu **primary** không khả dụng, **replica set** sẽ bầu chọn **secondary** và tiếp tục hoạt động . **Primary** cũ sẽ join lại sau khi khả dụng .

    <p align=center><img src=https://i.imgur.com/GF5s77Z.png width=40%></p>

#### **3.1.2) Kiến trúc Primary-Secondary-Arbiter (P-S-A)**
- Một **replica set** với 2 members lưu trữ data có :
    - 1 **primary**
    - 1 **secondary**. **Secondary** đều có thể trở thành **primary** sau khi bình chọn .
    - 1 **arbiter**. **Arbiter** sẽ chỉ vote khi bình chọn .

    <p align=center><img src=https://i.imgur.com/oGPYX16.png width=40%></p>

- Bởi vì **arbiter** không chứa bản copy data, cách deploy này chỉ cung cấp 1 bản copy hoàn chỉnh duy nhất. **Arbiter** yêu cầu ít tài nguyên hơn, nhưng tính dự phòng và khả năng chịu lỗi hạn chế hơn .
- Tuy nhiên, cách deploy này vẫn đảm bảo được tính sẵn sàng nếu **primary** hoặc **secondary** bị chết. Nếu **primary** chết, **replica set** sẽ bầu chọn **secondary** làm **primary** .

    <p align=center><img src=https://i.imgur.com/wXAGR4P.png width=40%></p>