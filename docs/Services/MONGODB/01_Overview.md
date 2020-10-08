# MongoDB
## **1) Tổng quan** <img src=https://i.imgur.com/Rd3vhTp.png width=15% align=right>
- **NoSQL** là 1 dạng CSDL mã nguồn mở không sử dụng **Transact-SQL** để truy vấn thông tin. **NoSQL** viết tắt bởi: ***None-Relational SQL***, hay có nơi thường gọi là ***Not-Only SQL***. CSDL này được phát triển trên Javascript Framework với kiểu dữ liệu **JSON**. **NoSQL** ra đời như là 1 mảnh vá cho những khuyết điểm và thiếu xót cũng như hạn chế của mô hình dữ liệu quan hệ **RDBMS** về tốc độ, tính năng, khả năng mở rộng, memory cache,...
- **MongoDB** là một cơ sở dữ liệu mã nguồn mở và là cơ sở dữ liệu **NoSQL** hàng đầu, được hàng triệu người sử dụng. **MongoDB** được viết bằng **C++**.
- Ngoài ra, **MongoDB** là một cơ sở dữ liệu đa nền tảng, hoạt động trên các khái niệm ***Collection*** và ***Document***, nó cung cấp hiệu suất cao, tính khả dụng cao và khả năng mở rộng dễ dàng.
- Trang chủ: https://www.mongodb.com/
- Source code: https://github.com/mongodb/mongo
- Phiên bản mới nhất hiện tại : `4.4.1` (phát hành ngày `9-9-2020`)
## **2) Các thuật ngữ hay sử dụng trong MongoDB**
- **`_id`** : Là trường bắt buộc có trong mỗi document. Trường `_id` đại diện cho một giá trị duy nhất trong document **MongoDB**. Trường `_id` cũng có thể được hiểu là ***primary key*** trong document. Nếu thêm mới một document thì **MongoDB** sẽ tự động sinh ra một `_id` đại diện cho **document** đó và là duy nhất trong cơ sở dữ liệu **MongoDB**.
- **Collection** : Là nhóm của nhiều **document** trong **MongoDB**. **Collection** có thể được hiểu là một **table** tương ứng trong cơ sở dữ liệu **RDBMS** (***Relational Database Management System***). **Collection** nằm trong một cơ sở dữ liệu duy nhất. Các **collection** không phải định nghĩa các cột, các hàng hay kiểu dữ liệu trước.
- **Cursor** : Là một con trỏ đến tập kết quả của một truy vấn. Máy khách có thể lặp qua một con trỏ để lấy kết quả.
- **Database** : Nơi chứa các **Collection**, giống với cơ sở dữ liệu **RDMS**, chúng chứa các **table**. Mỗi **Database** có một tập tin riêng lưu trữ trên bộ nhớ vật lý. Một mấy chủ **MongoDB** có thể chứa nhiều **Database**.
- **Document** : Một bản ghi thuộc một **Collection** thì được gọi là một **Document**. Các **Document** lần lượt bao gồm các trường key và value.
- **Field** : Là một cặp ***key - value*** trong một **document**. Một **document** có thể có không hoặc nhiều **field**. Các **field** giống các **column** ở cơ sở dữ liệu quan hệ.

| RDBMS | MongoDB | 
|-------|---------| 
| Database | Database | 
| Table | Collection| 
| Tuple/Row | Document| 
| Column | Field |
| Table Join | Embedded Documents |
 Primary Key | Primary Key (mặc định là `_id`) |
## **3) Lợi ích của MongoDB so với RDBMS**
- Ít cấu trúc (**schema**) hơn : **MongoDB** là một document database gồm các collection chứa cac document khác nhau. Số lượng field, content và kích cỡ của document này có thể khác với các document khác .
- Cấu trúc của một object rất rõ ràng .
- Join đơn giản hơn .
- Khả năng query sâu hơn. **MongoDB** hỗ trợ các câu query động sử dụng ***document-based query language*** gần tương đương như **SQL** .
- Cơ chế Tuning .
- Dễ dàng mở rộng (**scale-out**)
- Conversion/mapping of application objects to database objects not needed.
- Uses internal memory for storing the (windowed) working set, enabling faster access of data.

