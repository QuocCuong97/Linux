# Tổng quan về Redis
## **1) Giới thiệu**
- **Redis** là một cơ sở dữ liệu nâng cao mã nguồn mở và là một giải pháp để xây dựng các ứng dụng web có hiệu năng cao, tính mở rộng cao.
- Trang chủ: https://redis.io/
- GitHub: https://github.com/redis/redis
- Phiên bản release mới nhất: `6.0.8` (`9-9-2020`)
- **Redis** có 3 đặc điểm chính sau tạo nên khác biệt :
    - **Redis** lưu trữ toàn bộ database trong RAM, chỉ sử dụng disk cho các lưu trữ lâu dài.
    - **Redis** có một bộ kiểu dữ liệu phong phú so sánh với các cơ sở dữ liệu key-value khác.
    - **Redis** có thể replicate tới bất kể số lượng **slave** là bao nhiêu .
## **2) Lợi ích của Redis**
- Cực nhanh - **Redis** rất nhanh, có thể thực hiện khoảng `110000` `SET` mỗi giây, khoảng `81000 GET` mỗi giây.
- Hỗ trợ nhiều kiểu dữ liệu - **Redis** hỗ trợ nhiều nhất các kiểu dữ liệu mà developer đã biết như list, set, sorted set, hashes. Điều này giúp dễ dàng giải quyết các vấn đề tốt nhất với kiểu dữ liệu thích hợp .
- Các hoạt động trong **Redis** ở mức "nguyên tử" - điều này đảm bảo nếu có 2 client truy cập đồng thời, server **Redis** sẽ nhận được giá trị cập nhật .
- Là một công cụ đa tiện ích - **Redis** là một công cụ đa tiện ích và có thể được sử dụng trong một số trường hợp được sử dụng như bộ nhớ đệm, message queue (**redis** hỗ trợ publish/subcribe), bất kỳ dữ liệu ngắn hạn nào trong ứng dụng, chẳng hạn như session, số lần truy cập web,...
## **3) Cơ chế lưu trữ**
### **3.1) In memory**
- Không như các DBMS khác lưu trữ dữ liệu trên đĩa cứng, **Redis** lưu trữ dữ liệu trên RAM, và đương nhiên là thao tác đọc/ghi trên RAM. Thao tác trên RAM nhanh hơn nhiều so với trên ổ cứng, tuy nhiên dữ liệu cũng sẽ bị mất khi server bị tắt
### **3.2) Persistent redis**
- Dù làm việc với data dạng key-value lưu trữ trên RAM, Redis vẫn cần lưu trữ dữ liệu trên ổ cứng. 1 là để đảm bảo toàn vẹn dữ liệu khi có sự cố xảy ra (server bị tắt nguồn) cũng như tái tạo lại dataset khi restart server, 2 là để gửi data đến các slave server, phục vụ cho tính năng replication. 
- **Redis** cung cấp 2 phương thức chính cho việc sao lưu dữ liệu ra ổ cứng, đó là **RDB - *(Redis DataBase file)*** và **AOF - *(Append only file)***
#### **3.2.1) RDB**
- Cách thức làm việc :
    - **RDB** thực hiện tạo và sao lưu snapshot của DB vào ổ cứng sau mỗi khoảng thời gian nhất định.
- Ưu điểm :
    - **RDB** cho phép người dùng lưu các version khác nhau của DB, rất thuận tiện khi có sự cố xảy ra.
    - Bằng việc lưu trữ data vào 1 file cố định, người dùng có thể dễ dàng chuyển data đến các data centers khác nhau, hoặc chuyển đến lưu trữ trên Amazon S3.
    - RDB giúp tối ưu hóa hiệu năng của Redis. Tiến trình Redis chính sẽ chỉ làm các công việc trên RAM, bao gồm các thao tác cơ bản được yêu cầu từ phía client như thêm/đọc/xóa, trong khi đó 1 tiến trình con sẽ đảm nhiệm các thao tác disk I/O. Cách tổ chức này giúp tối đa hiệu năng của Redis.
    - Khi restart server, dùng RDB làm việc với lượng data lớn sẽ có tốc độ cao hơn là dùng AOF.
- Nhược điểm :
    - **RDB** không phải là lựa chọn tốt nếu muốn giảm thiểu tối đa nguy cơ mất mát dữ liệu. Thông thường người dùng sẽ set up để tạo **RDB** snapshot 5 phút 1 lần (hoặc nhiều hơn). Do vậy, trong trường hợp có sự cố, **Redis** không thể hoạt động, dữ liệu trong những phút cuối sẽ bị mất.
    - **RDB** cần dùng `fork()` để tạo tiến trình con phục vụ cho thao tác disk I/O. Trong trường hợp dữ liệu quá lớn, quá trình `fork()` có thể tốn thời gian và server sẽ không thể đáp ứng được request từ client trong vài milisecond hoặc thậm chí là 1 second tùy thuộc vào lượng data và hiệu năng CPU.

        <img src=https://i.imgur.com/mzPBfnO.png>

    - Như minh họa ở trên, trong trường hợp không có bản sao của tập dữ liệu còn lại trong RAM, Redis Enterprise sẽ tìm bản sao cuối cùng của tập dữ liệu trong các thiết bị được kết nối mạng được kết nối với nút không thành công và sử dụng nó để điền phân đoạn Redis trên phiên bản đám mây mới.
#### **3.2.2) AOF**
- Cách thức làm việc :
    - **AOF** lưu lại tất cả các thao tác write mà server nhận được, các thao tác này sẽ được chạy lại khi restart server hoặc tái thiết lập dataset ban đầu.
- Ưu điểm :
    - Sử dụng AOF sẽ giúp đảm bảo dataset được bền vững hơn so với dùng RDB. Người dùng có thể config để Redis ghi log theo từng câu query hoặc mỗi giây 1 lần.
    - Redis ghi log AOF theo kiểu thêm vào cuối file sẵn có, do đó tiến trình seek trên file có sẵn là không cần thiết. Ngoài ra, kể cả khi chỉ 1 nửa câu lệnh được ghi trong file log (có thể do ổ đĩa bị full), Redis vẫn có cơ chế quản lý và sửa chữa lối đó (redis-check-aof).
    - Redis cung cấp tiến trình chạy nền, cho phép ghi lại file AOF khi dung lượng file quá lớn. Trong khi server vẫn thực hiện thao tác trên file cũ, 1 file hoàn toàn mới được tạo ra với số lượng tối thiểu operation phục vụ cho việc tạo dataset hiện tại. Và 1 khi file mới được ghi xong, Redis sẽ chuyển sang thực hiện thao tác ghi log trên file mới.
- Nhược điểm :
    - File AOF thường lớn hơn file RDB với cùng 1 dataset.
    - AOF có thể chậm hơn RDB tùy theo cách thức thiết lập khoảng thời gian cho việc sao lưu vào ổ cứng. Tuy nhiên, nếu thiết lập log 1 giây 1 lần có thể đạt hiệu năng tương đương với RDB.
    - Developer của Redis đã từng gặp phải bug với AOF (mặc dù là rất hiếm), đó là lỗi AOF không thể tái tạo lại chính xác dataset khi restart Redis. Lỗi này chưa gặp phải khi làm việc với RDB bao giờ.
    - Câu hỏi đặt ra là, chúng ta nên dùng RDB hay AOF? Mỗi phương thức đều có ưu/nhược điểm riêng, và có lẽ cần nhiều thời gian làm việc với Redis cũng như tùy theo ứng dụng mà đưa ra lựa chọn thích hợp. Nhiều người chọn AOF bới nó đảm bảo toàn vẹn dữ liệu rất tốt, nhưng Redis developer lại khuyến cáo nên dùng cả RDB, bởi nó rất thuận tiện cho việc backup database, tăng tốc độ cho quá trình restart cũng như tránh được bug của AOF.
    - Cũng cần lưu ý thêm rằng, Redis cho phép không sử dụng tính năng lưu trữ thông tin trong ổ cứng (không RDB, không AOF), đồng thời cũng cho phép dùng cả 2 tính năng này trên cùng 1 instance. Tuy nhiên khi restart server, Redis sẽ dùng AOF cho việc tái tạo dataset ban đầu, bới AOF sẽ đảm bảo không bị mất mát dữ liệu tốt hơn là RDB.