# MariaDB
## **1) Tổng quan** <img src=https://i.imgur.com/dnuMPbp.png align=right width=30%>
- **MariaDB** là một phiên bản nhánh khác của mã nguồn dịch vụ **MySQL** ( **MySQL** thuộc sở hữu của **Oracle** ) , là máy chủ cơ sở dữ liệu cung cấp các chức năng thay thế cho **MySQL** .
- **MariaDB** phát hành phiên bản đầu tiên vào tháng `11/2008` bởi `Monty Widenius` , người đồng sáng lập **MySQL** . `Widenius` sau khi nghỉ công tác hco **MySQL** ( sau khi **Sun** mua lại **MySQL** ) đã thành lập công ty **Monty Program AB** và phát triển **MariaDB** .
- **MySQL** là 1 phần mềm mã nguồn mở miễn phí , sử dụng giấy phép **GNU Genenal Public License - GPLv2** .
- Trang chủ : https://mariadb.org/
- Source Code : https://github.com/MariaDB/server
- Phiên bản đầu tiên phát hành `29-10-2009`
- Phiên bản mới nhất hiện tại : `10.4.7` ( phát hành `31-7-2019` )
- Là 1 thành phần của **LAMP Stack** - ( `Linux` , `Apache` , `MariaDB` , `Perl/Python/PHP` )
## **2) So sánh MariaDB và MySQL**
| | **MariaDB** | **MySQL** |
|-|-------------|-----------|
| **Nhà phát triển** | **MariaDB Foundation** | **Oracle** |
| **Phát hành lần đầu** | `2009` | `1995` |
| **Phát hành hiện tại** | `10.4.7`<br>( `23-5-2019` ) | `8.0.17`<br>( `22-7-2019` ) |
| **Giấy phép** | OpenSource | OpenSource + Commercial |
| **Phát triển** | Mở | Đóng |
| **Công cụ lưu trữ** | `InnoDB` , `MyISAM` , `BLACKHOLE` , `CSV` , `MEMORY` , `ARCHIVE` , `MERGE` , `ColumnStore` , `MyRocks` , `Aria` , `SphinxSE` , `TokuDB` , `CONNECT` , `SEQUENCE` , `Spider` , `Cassandra` | `InnoDB` , `MyISAM` , `BLACKHOLE` , `CSV` , `MEMORY` , `ARCHIVE` , `MERGE` |
| **Quản lý SQL** | `HeidiSQL` | `MySQL Workbench` |
| **Ngôn ngữ triển khai** | `C` và `C++` | `C` và `C++` |
| **Hệ điều hành máy chủ** | **FreeBSD** , **Linux** , **Solaris** , **Windows** | **FreeBSD** , **Linux** , **OS X** , **Solaris** , **Windows** |
| **Thị trường** | **MariaDB** mặc định trong **LAMP** | **MySQL** mặc định trong **AppServ** |

>### **Ưu điểm vượt trội của MariaDB**
- Được bảo trì và phát triển bởi chính người tạo ra **MySQL** .
- Hoàn toàn tương thích với **MySQL** .
- Thêm nhiều tính năng hay và nhiều engine lưu trữ 
- Sử dụng **MariaDB** để tối ưu tốc độ : nhanh hơn **MySQL** từ `3-5%` và sẽ được tối ưu ngày càng tốt hơn .
- Các website lớn đã và đang chuyển sang sử dụng **MariaDB**

    <img src=https://i.imgur.com/xvdxJ3F.png>

## **3) Cấu hình mẫu dịch vụ MariaDB**