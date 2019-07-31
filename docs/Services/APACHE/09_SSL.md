# Cấu hình SSL/TLS trên Apache
## **1) Khái niệm**
- **SSL - Secure Sockets Layer** là một loại bảo mật giúp mã hóa liên lạc giữa website và trình duyệt . Công nghệ này đang lỗi thời và được thay thế hoàn toàn bởi **TLS** .
- **TLS - Transport Layer Security** cũng giúp bảo mật thông tin truyền giống như **SSL** . Nhưng vì **SSL** không còn được phát triển nữa , nên **TLS** mới là thuật ngữ đúng nên dùng .
- **HTTPS** là phần mở rộng bảo mật của **HTTP** . Website được cài đặt chứng chỉ **SSL/TLS** có thể dùng giao thức **HTTPS** để thiết lập kênh kết nối an toàn tới server .
- Mục tiêu của **SSL/TLS** là bảo mật các thông tin nhạy cảm trong quá trình truyền trên internet như thông tin cá nhân , thông tin thanh toán , thông tin đăng nhập ....
- Nó là giải pháp thay thế cho phướng pháp truyền thông tin văn bản dạng ***plain text*** , văn bản loại này khi truyền trên internet sẽ không được mã hóa , nên việc áp dụng mã hóa vào sẽ khiến cho các bên thứ 3 không xâm nhập được vào thông tin của bạn , không đánh cắp hay chỉnh sửa được các thông tin đó .
- Hầu hết mọi người đều quen thuộc với các chứng chỉ **SSL/TLS** , đang được dùng bởi các website lớn và các webmaster nghiêm túc trong việc bảo vệ các giao dịch người dùng .
- Có thể biết được website có đang dùng chứng chỉ bảo mật **SSL/TLS** hay không đơn giản bằng cách nhìn vào icon trong URL ngay trong thanh địa chỉ :

    <img src=https://i.imgur.com/WbYzQRb.png>
    <img src=https://i.imgur.com/YQ8Yzv2.png>

## **2) Hoạt động của SSL/TLS**