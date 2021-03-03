# Local Repository
- Trong trường hợp server không có kết nối internet hoặc sử dụng **RedHat** nhưng chưa có license, ta cần cấu hình **local repository** dựa trên file `.iso` full của distro để có thể cài đặt được các package qua lệnh `yum` .
### **Các bước thực hiện**
- **B1 :** Download các package cần thiết và cài đặt :
    - `deltaprm` (dependency):
        ```
        # curl http://mirror.centos.org/centos/7/os/x86_64/Packages/deltarpm-3.6-3.el7.x86_64.rpm -o deltarpm-3.6-3.el7.x86_64.rpm
        # rpm -Uvh deltarpm-3.6-3.el7.x86_64.rpm
        ```
    - `python-deltarpm` (dependency):
        ```
        # curl http://mirror.centos.org/centos/7/os/x86_64/Packages/python-deltarpm-3.6-3.el7.x86_64.rpm -o python-deltarpm-3.6-3.el7.x86_64.rpm
        # rpm -Uvh python-deltarpm-3.6-3.el7.x86_64.rpm
        ```
    - `createrepo`
        ```
        # curl http://mirror.centos.org/centos/7/os/x86_64/Packages/createrepo-0.9.9-28.el7.noarch.rpm -o createrepo-0.9.9-28.el7.noarch.rpm
        # rpm -Uvh createrepo-0.9.9-28.el7.noarch.rpm
        ```
- **B2 :** Mount file cài đặt `.iso` của distro vào `/mnt` :
    ```
    # mount -r -t iso9660 /dev/sr0 /mnt
    ```
- **B3 :** Copy toàn bộ các package trong thư mục vừa mount sang thư mục `/etc/repo` :
    ```
    # mkdir -p /etc/repo
    # cp -ar /mnt/Packages/* /etc/repo/
    ```
- **B4 :** Khởi tạo repo :
    ```
    # cd /etc/repo
    # createrepo -v /etc/repo
    # vi /etc/yum.repos.d/rhel.repo
    ```
    - Thêm vào file nội dung sau :
        ```
        [rhel]
        name=rhel
        baseurl=file:///etc/repo
        enabled=1
        gpgcheck=0
        ```
- **B5 :** Xóa cache và update lại list các repo :
    ```
    # yum clean all
    # yum repolist
    ```
> Đến đây đã có thể sử dụng lệnh `yum` để cài đặt. Tuy nhiên các package khi cài sẽ chỉ update tới đúng version chứa trong file `.iso` của distro