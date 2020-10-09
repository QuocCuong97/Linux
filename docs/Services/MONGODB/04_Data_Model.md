# Data Model
- **MongoDB** cung cấp 2 kiểu data model: ***Embeded*** và ***Nomalized***. Tùy vào yêu cầu, có thể sử dụng model thích hợp .
### **Embeded data model**
- Với loại model này, ta có thể nhúng các dữ liệu liên quan vào chung một document . Loại này cũng được gọi là ***de-normalized data model*** .
- **VD :**
    ```
    {
        _id: ,
        Emp_ID: "10025AE336"
        Personal_details:{
            First_Name: "Radhika",
            Last_Name: "Sharma",
            Date_Of_Birth: "1995-09-26"
        },
        Contact: {
            e-mail: "radhika_sharma.123@gmail.com",
            phone: "9848022338"
        },
        Address: {
            city: "Hyderabad",
            Area: "Madapur",
            State: "Telangana"
        }
    }
    ```
### **Normalized data model**
- Với loại model này, ta có thể liên kết các sub-document trong 1 document.
- **VD :**
    - Document 1 :
        ```
        {
            _id: <ObjectId101>,
            Emp_ID: "10025AE336"
        }
        ```
    - Liên kết document 1 trong document 2 :
        ```
        {
            _id: <ObjectId102>,
            empDocID: " ObjectId101",
            First_Name: "Radhika",
            Last_Name: "Sharma",
            Date_Of_Birth: "1995-09-26"
        }
        ```
    - Liên kết document 1 trong document 3 :
        ```json
        {
            _id: <ObjectId103>,
            empDocID: " ObjectId101",
            e-mail: "radhika_sharma.123@gmail.com",
            phone: "9848022338"
        }
        ```
    - Hay trong cả document 4 :
        ```json
        {
            _id: <ObjectId104>,
            empDocID: " ObjectId101",
            city: "Hyderabad",
            Area: "Madapur",
            State: "Telangana"
        }
        ```
### **Thiết kế schema trong MongoDB**
- Thiết kế schema dựa trên các yêu cầu của người dùng.
- Chỉ nên gộp các object vào trong 1 document nếu sẽ sử dụng chúng chung với nhau. Nếu không, nên tách riêng chúng ra .
- Có thể thoải mái nhân bản (duplicate) dữ liệu nếu cần thiết để tiện cho việc tính toán, vì dung lượng đĩa sẽ rẻ hơn là thời gian tính toán.
- Do joins while write, not on read.
- Tối ưu schema cho trường hợp thường sử dụng nhất .
- Do complex aggregation in the schema.
- **VD :** Giả sử một khách hàng cần thiết kế một database cho website/blog và thấy được điểm khác nhau giữa RDBMS và thiết kế schema MongoDB. Website cần có các yêu cầu sau :
    - Tất cả các bài viết phải có title, description và url riêng
    - Tất cả các bài viết được gắn 1 hoặc nhiều tag
    - Tất cả các bài viết phải có tên người viết và số lượt like
    - Tất cả các bài viết có comment phải đi kèm với tên, messages, thời gian comment và likes
- Trong thiết kế RDBMS, để đáp ứng tất cả các yêu cầu trên cần tối thiểu 3 tables :

    <img src=https://i.imgur.com/cAniQfH.png>

- Trong thiết kế schema của mongoDB, chỉ cần 1 collection `post` theo cấu trúc sau :
    ```json
    {
        _id: POST_ID
        title: TITLE_OF_POST, 
        description: POST_DESCRIPTION,
        by: POST_BY,
        url: URL_OF_POST,
        tags: [TAG1, TAG2, TAG3],
        likes: TOTAL_LIKES, 
        comments: [	
            {
                user:'COMMENT_BY',
                message: TEXT,
                dateCreated: DATE_TIME,
                like: LIKES 
            },
            {
                user:'COMMENT_BY',
                message: TEXT,
                dateCreated: DATE_TIME,
                like: LIKES
            }
        ]
    }
    ```
- Kết luận : Khi truy vấn dữ liệu, trong **RDBMS** ta cần join 3 table lại với nhau, còn trong **MongoDB**, data sẽ chỉ ở trong 1 collection duy nhất .
