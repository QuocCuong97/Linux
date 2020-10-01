# Tổng quan về MongoDB
## **1) Khái niệm**
- **NoSQL** là 1 dạng CSDL mã nguồn mở không sử dụng **Transact-SQL** để truy vấn thông tin. **NoSQL** viết tắt bởi: ***None-Relational SQL***, hay có nơi thường gọi là ***Not-Only SQL***. CSDL này được phát triển trên Javascript Framework với kiểu dữ liệu **JSON**. **NoSQL** ra đời như là 1 mảnh vá cho những khuyết điểm và thiếu xót cũng như hạn chế của mô hình dữ liệu quan hệ **RDBMS** về tốc độ, tính năng, khả năng mở rộng, memory cache,...
- **MongoDB** là một cơ sở dữ liệu mã nguồn mở và là cơ sở dữ liệu **NoSQL** hàng đầu, được hàng triệu người sử dụng. **MongoDB** được viết bằng **C++**.
- Ngoài ra, **MongoDB** là một cơ sở dữ liệu đa nền tảng, hoạt động trên các khái niệm ***Collection*** và ***Document***, nó cung cấp hiệu suất cao, tính khả dụng cao và khả năng mở rộng dễ dàng.
## **2) Các thuật ngữ hay sử dụng trong MongoDB**
- **`_id`** : Là trường bắt buộc có trong mỗi document. Trường _id đại diện cho một giá trị duy nhất trong document MongoDB. Trường _id cũng có thể được hiểu là khóa chính trong document. Nếu bạn thêm mới một document thì MongoDB sẽ tự động sinh ra một _id đại diện cho document đó và là duy nhất trong cơ sở dữ liệu MongoDB.
- Collection – Là nhóm của nhiều document trong MongoDB. Collection có thể được hiểu là một bảng tương ứng trong cơ sở dữ liệu RDBMS (Relational Database Management System). Collection nằm trong một cơ sở dữ liệu duy nhất. Các collection không phải định nghĩa các cột, các hàng hay kiểu dữ liệu trước.
- Cursor – Đây là một con trỏ đến tập kết quả của một truy vấn. Máy khách có thể lặp qua một con trỏ để lấy kết quả.
- Database – Nơi chứa các Collection, giống với cơ sở dữ liệu RDMS chúng chứa các bảng. Mỗi Database có một tập tin riêng lưu trữ trên bộ nhớ vật lý. Một mấy chủ MongoDB có thể chứa nhiều Database.
- Document – Một bản ghi thuộc một Collection thì được gọi là một Document. Các Document lần lượt bao gồm các trường tên và giá trị.
- Field – Là một cặp name – value trong một document. Một document có thể có không hoặc nhiều trường. Các trường giống các cột ở cơ sở dữ liệu quan hệ.
- JSON – Viết tắt của JavaScript Object Notation. Con người có thể đọc được ở định dạng văn bản đơn giản thể hiện cho các dữ liệu có cấu trúc. Hiện tại JSON đang hỗ trợ rất nhiều ngôn ngữ lập trình.
- Index – Là những cấu trúc dữ liệu đặc biệt, dùng để chứa một phần nhỏ của các tập dữ liệu một cách dễ dàng để quét. Chỉ số lưu trữ giá trị của một fields cụ thể hoặc thiết lập các fields, sắp xếp theo giá trị của các fields này. Index hỗ trợ độ phân tích một cách hiệu quả các truy vấn. Nếu không có chỉ mục, MongoDB sẽ phải quét tất cả các documents của collection để chọn ra những document phù hợp với câu truy vấn. Quá trình quét này là không hiệu quả và yêu cầu MongoDB để xử lý một khối lượng lớn dữ liệu.
## **3) So sánh RDBMS và MongoDB**
- 