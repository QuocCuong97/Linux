# Sử dụng thư viện Pika của Python để làm việc với RabbitMQ
- Ở ví dụ này, ta sẽ viết 2 chương trình nhỏ bằng **Python**, một là **Producer** (sender) gửi message, và một **Consumer** (receiver) nhận message và `print` nó ra.
- Mô hình logic :
    <p align=center><img src=https://www.rabbitmq.com/img/tutorials/python-one-overall.png></p>

    > **P** là **producer** và **C** là **consumer**. Box ở giữa là một **queue**. **P** sẽ gửi message đến 'hello' queue. **C** nhận message từ **queue** .
- Mô hình vật lý :
    <p align=center><img src=https://i.imgur.com/K21QQl6.png></p>
    
- Cài đặt **Pika** :
    ```
    # pip install pika
    ```
### **Quá trình gửi**
- Khởi tạo kết nối đến **RabbitMQ** :
    ```py
    import pika

    params = pika.URLParameters('amqp://mqadmin:Password123@192.168.5.10:5672')
    connection = pika.BlockingConnection(parameters=params)
    channel = connection.channel()
    ```
    hoặc
    ```py
    connection = pika.BlockingConnection(pika.ConnectionParameters('192.168.5.10', 5672, '/', pika.PlainCredentials('mqadmin', 'Password123')))
    ```
- Tạo queue. Nếu gửi message đến một queue không tồn tại, **RabbitMQ** sẽ drop message đó .
    ```py
    channel.queue_declare(queue='hello')
    ```
    - Kiểm tra lại queue đã tạo trên **RabbitMQ server** :
        ```
        # rabbitmqctl list_queues
        Listing queues
        hello   0
        ```
        <img src=https://i.imgur.com/2k2Bbw9.png>

- Trong **RabbitMQ**, một message không bao giờ được gửi thẳng đến **queue**, mà nó luôn cần một exchange. Exchange rất đặc biệt - nó cho phép chỉ định chính xác queue mà message sẽ tới. Tên của queue cần được chỉ định trong tham số `routing_key` :
    ```py
    channel.basic_publish(exchange='',
                        routing_key='hello',
                        body='Hello World!')
    ```
- Đóng kết nối **RabbitMQ** :
    ```py
    channel.close()
    ```
- File hoàn chỉnh :
    ```py
    # send.py
    import pika

    params = pika.URLParameters('amqp://mqadmin:Password123@192.168.5.10:5672')
    connection = pika.BlockingConnection(parameters=params) 
    channel = connection.channel()

    channel.queue_declare(queue='hello')

    channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
    print(" [x] Sent 'Hello World!'")
    connection.close()
    ```
    - Kết quả :
        ```
        # python3 send.py
        [x] Sent 'Hello World!'
        ```
### **Quá trình nhận**
- Khởi tạo kết nối đến **RabbitMQ** :
    ```py
    import pika

    params = pika.URLParameters('amqp://mqadmin:Password123@192.168.5.10:5672')
    connection = pika.BlockingConnection(parameters=params)
    channel = connection.channel()

    channel.queue_declare(queue='hello')
    ```
- Các bước nhận message từ **queue** sẽ phức tạp hơn. Một hàm callback sẽ được đăng ký trước. Khi nhận về được message, hàm callback đó sẽ được gọi lên bởi `pika` .
    ```py
    def callback(ch, method, properties, body):
        print(" [x] Received %r" % body)
    ```
- Lấy message về :
    ```py
    channel.basic_consume(queue='hello',
                          auto_ack=True,
                          on_message_callback=callback)
    ```
- File hoàn chỉnh :
    ```py
    # receive.py
    import pika, sys, os

    def main():
        connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
        channel = connection.channel()

        channel.queue_declare(queue='hello')

        def callback(ch, method, properties, body):
            print(" [x] Received %r" % body)

        channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=True)

        print(' [*] Waiting for messages. To exit press CTRL+C')
        channel.start_consuming()

    if __name__ == '__main__':
        try:
            main()
        except KeyboardInterrupt:
            print('Interrupted')
            try:
                sys.exit(0)
            except SystemExit:
                os._exit(0)
    ```
    - Kết quả :
        ```
        [*] Waiting for messages. To exit press CTRL+C
        [x] Received b'Hello World!'
        ```
