# Các kiểu dữ liệu trong MongoDB
- **MongoDB** hỗ trợ nhiều kiểu dữ liệu, điển hình là :
    - **String** : Đây là kiểu dữ liệu được sử dụng phổ biến nhất để lưu trữ dữ liệu. **String** trong **MongoDB** phải là `UTF-8` hợp lệ.
    - **Integer** : Kiểu dữ liệu này được sử dụng để lưu một giá trị số. Số nguyên có thể là 32 bit hoặc 64 bit phụ thuộc vào Server của bạn.
    - **Boolean** : Kiểu dữ liệu này được sử dụng để lưu giữ một giá trị Boolean (true/false).
    - **Double** : Kiểu dữ liệu này được sử dụng để lưu các giá trị số thực, dấu chấm động.
    - **Min/ Max keys** : Kiểu dữ liệu này được sử dụng để so sánh một giá trị với các phần tử BSON thấp nhất và cao nhất.
    - **Array**: Kiểu dữ liệu này được sử dụng để lưu giữ các mảng hoặc danh sách hoặc nhiều value vào trong một key.
    - **Timestamp** : Đánh dấu thời điểm một Document được sửa đổi hoặc được thêm vào.
    - **Object** : Kiểu dữ liệu này được sử dụng cho các Document được nhúng vào.
    - **Null** : Kiểu dữ liệu này được sử dụng để lưu một giá trị `Null`.
    - **Symbol** : Kiểu dữ liệu này được sử dụng giống như một **string**
    - **Date** : Kiểu dữ liệu này được sử dụng để lưu giữ date và time hiện tại trong định dạng UNIX time.
    - **Object ID**: Kiểu dữ liệu này được sử dụng để lưu giữ ID của Document.
    - **Binary data** : Kiểu dữ liệu này được sử dụng để lưu giữ dữ liệu nhị phân.
    - **Code** : Kiểu dữ liệu này được sử dụng để lưu giữ JavaScrip code vào trong Document.
    - **Regular expression** : Kiểu dữ liệu này được sử dụng để lưu giữ Regular Expresion.