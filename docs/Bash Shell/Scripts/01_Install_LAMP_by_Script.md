# Cài đặt LAMP bằng Script
- **B1 :** Download script :
    ```
    # cd ~/Desktop
    # wget https://raw.githubusercontent.com/QuocCuong97/Public_Zone/master/lamp.sh
    ```
    - Nội dung script :
        ```bash
        #!/bin/bash
        install_httpd(){
            yum install -y httpd
            systemctl start httpd
            systemctl enable httpd
            firewall-cmd --zone=public --permanent --add-service=http
            firewall-cmd --reload
        }
        install_mariadb(){
        echo "[mariadb]
        name = MariaDB
        baseurl = http://yum.mariadb.org/10.4.7/centos7-amd64
        gpgkey=http://yum.mariadb.org/RPM-GPG-KEY-MariaDB
        gpgcheck=1" > /etc/yum.repos.d/MariaDB.repo
            yum repolist
            yum install MariaDB-server MariaDB-client MariaDB-devel -y
            systemctl start mariadb.service
            systemctl enable mariadb.service
        }
        install_php(){
            yum install -y epel-release
            yum install -y yum-utils
            wget https://rpms.remirepo.net/enterprise/remi-release-7.rpm
            rpm -Uvh remi-release-7.rpm
            yum-config-manager --disable remi-php54
            yum-config-manager --enable remi-php73
            yum install php -y
            echo "<?php
            phpinfo();
            ?>" > /var/www/html/info.php
            systemctl restart httpd
        }

        clear
        printf "=========================================================================\n"
        printf "*******************LAMP Installation - Edited by Cuo**********************n"
        printf "=========================================================================\n"
        printf "First Step: Install Apache 2.4.6\n"
        printf "====================================\n"
        install_httpd

        clear
        printf "=========================================================================\n"
        printf "Second Step: Install MariaDB 10.4.7\n"
        printf "=======================================\n"
        install_mariadb

        clear
        printf "=========================================================================\n"
        printf "Last Step: Install PHP 7.3.8\n"
        printf "================================\n"
        install_php

        printf "=========================================================================\n"
        printf "Install successfully , enjoy LAMP! \n"
        printf "=========================================================================\n"
        ```
- **B2 :** Cấp quyền thực thi cho script :
    ```
    # chmod 755 ~/Desktop/lamp.sh
    ```
- **B3 :** Chạy script :
    ```
    # ./lamp.sh