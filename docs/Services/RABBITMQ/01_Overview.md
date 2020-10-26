# Tổng quan về RabbitMQ
## **1) Message Broker**
- **Message broker** (hay còn gọi là **integration broker** hoặc **interface engine**) là một module trung gian trung chuyển message từ người gửi đến người nhận. 
- Nó là một mô hình kiến trúc (*architectural pattern*) để kiểm tra, trung chuyển và điều hướng message; làm trung gian giữa các ứng dụng với nhau, tối giản hóa giao tiếp giữa các ứng dụng đó và để tăng hiệu quả tối đa cho việc tách ra các khối nhỏ hơn. 
- Nhiệm vụ chính của một **Message broker** là tiếp nhận những message từ các ứng dụng và thực hiện một thao tác nào đó.
- **VD :** Có diagram sau :
    <p align=center><img src=https://i.imgur.com/CL3CAl4.png width=50%></p>

    - Như ta có thể thấy, trong quá trình *Initialize*, *Service1 và Sevice2 init*, rồi sau đó *load proxy* và *register* đến Broker. Từ đó, Broker sẽ trung chuyển các message đến với proxy đã được register từ trước. Pattern này sẽ có những lợi ích sau:
        - Service1 và Service2 không cần phải biết nhau. Nó chỉ việc gửi message đến proxy, rồi từ đó proxy sẽ forward message đến Broker. Rồi từ đó Broker sẽ forward message đến Service1 và Service2 mà chúng đã đăng kí nhận message từ trước.
        - Service1 và Service2 giao tiếp trung gian qua Broker nên dù có khác nhau về ngôn ngữ thì vẫn giao tiếp thành công.
    - Với design pattern này, chúng ta có thể setup cơ chế bất đồng bộ (asynchronous). Đối với Service1 thì nó không cần quan tâm khi nào message đến tay Service2 hay khi nào Service2 xử lý xong, nó chỉ cần đấy message đến Message Broker là xong việc. Service2 sẽ lấy message bất cứ khi nào nó muốn. Đặc tính này có thể được tận dụng để xây dựng các hệ thống lưu trữ và xử lý log.
- Hiện tại có rất nhiều các **message broker software** có thể kể đến như: **Amazon Web Services (AWS) Simple Queue Service (SQS)**, **Apache Kafka**, **Apache ActiveMQ**. Nhưng phổ biến nhất trong số những cái tên kể trên đó là **RabbitMQ** .
## **2) RabbitMQ**
- **RabbitMQ** là một **message broker *open-source***, ban đầu được dùng cho **Advanced Message Queuing Protocol (AMQP)**, sau đó đã được phát triển để hỗ trợ **Streaming Text Oriented Messaging Protocol (STOMP)**, **Message Queuing Telemetry Transport (MQTT)** . 
- **RabbitMQ** được viết bằng **Erlang**, một ngôn ngữ không phổ biến nhưng khá phù hợp với các công việc của **message broker** .
- Một số thuật ngữ thường dùng :
    - **Producing** : gửi. Ứng dụng gửi message được gọi là **Producer**.
        <p align=center><img src=https://www.rabbitmq.com/img/tutorials/producer.png></p>

    - **Queue** là một ***post box*** nằm trong **RabbitMQ**. Message di chuyển qua **RabbitMQ** và ứng dụng của bạn nhưng chúng chỉ có thể được lưu trong **queue**. **Queue** được giới hạn trong memory và disk của host. Về bản chất, nó là một bộ nhớ đệm message với dữ liệu lớn. Nhiều **producer** có thể gửi message vào một **queue** và nhiều **consumer** có thể nhận data từ một **queue**:
        <p align=center><img src=https://www.rabbitmq.com/img/tutorials/queue.png></p>

    - **Consuming** : nhận. **Consumer** là một ứng dụng chủ yếu chờ để nhận message:
        <p align=center><img src=https://www.rabbitmq.com/img/tutorials/consumer.png></p>

    > **Producer**, **consumer** và **broker** không cần phải phụ thuộc vào cùng một host. Trên thực tế rất hiếm có những ứng dụng như vậy. Một ứng dụng cũng có thể vừa là **producer** và vừa là **consumer** .
- Các port được sử dụng bởi **RabbitMQ**
    - Các giao tiếp trong node và CLI sử dụng port `25672`
    - **AMQP 0-9-1** lắng nghe các kết nối non-TLS sử dụng port `5672`
    - **AMQP 0-9-1** lắng nghe các kết nối TLS sử dụng port `5671`
    - **HTTP API** sử dụng port `15672` (**HTTP**) và `15671` (**HTTPS**)
    - **MQTT** lắng nghe các kết nối non-TLS sử dụng port `1883`
