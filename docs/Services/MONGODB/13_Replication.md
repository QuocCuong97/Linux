# Replication
## **1) Khái niệm Replication**
- **Replication** là quá trình đồng bộ dữ liệu giữa nhiều server.
- **Replication** cung cấp tính dự phòng và tăng tính sẵn sàng cho dữ liệu bằng việc tạo ra nhiều bản copy của dữ liệu trên các database server khác nhau .



Replication is the process of synchronizing data across multiple servers. Replication provides redundancy and increases data availability with multiple copies of data on different database servers. Replication protects a database from the loss of a single server. Replication also allows you to recover from hardware failure and service interruptions. With additional copies of the data, you can dedicate one to disaster recovery, reporting, or backup.
## **2) Cách làm việc của replication trong MongoDB**
- Một **replica set** trong **MongoDB** là một nhóm các process của **mongodb** duy trì cùng một bộ dữ liệu. 
- Các **replica set** cung cấp tính dự phòng và tính sẵn sàng cao và là cơ sở để triển khai nhập xuất dữ liệu khi cần thiết