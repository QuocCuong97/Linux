# Một số lệnh cơ bản
- Để chạy các lệnh trên **Redis server**, cần có **redis client** (đã đi kèm khi cài **redis**)
- Cú pháp :
    ```
    # redis-cli
    ```
    - Lệnh này sẽ kết nối tới local **redis server**. Ta có thể kiểm tra dịch vụ có chạy không bằng lệnh `PING` :

        <img src=https://i.imgur.com/u7DKvjT.png>
### **Kết nối tới remote server**
- Cú pháp :
    ```
    # redis-cli -h HOST -p POST -a PASSWORD
    ```