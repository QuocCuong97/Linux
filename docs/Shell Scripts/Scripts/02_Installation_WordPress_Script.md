# Cài đặt WordPress qua Script
- **B1 :** Download script :
    ```
    # cd ~/Desktop
    # wget https://raw.githubusercontent.com/QuocCuong97/Public_Zone/master/wordpress.sh
    ```
    - Nội dung script :
        ```bash
        #!/bin/bash
        ## Install Wordpress on CentOS7
        DIRECTORY=$(cd `dirname $0` && pwd)
        create_database(){
            echo -n "MariaDB Host (localhost): "
            read mariahost
            if [ "$mysqlhost" = "" ]
            then
            mariahost="localhost"
            fi
            echo -n "New MariaDB Name: "
            read mariadb

            echo -n "New MariaDB User: "
            read mariauser

            echo -n "Password: "
            read mariapass

        mysql -u root <<MYSQL_SCRIPT
        CREATE DATABASE $mariadb;
        CREATE USER $mariauser@$mariahost IDENTIFIED BY '$mariapass';
        GRANT ALL PRIVILEGES ON $mariadb.* TO $mariauser IDENTIFIED BY '$mariapass';
        FLUSH PRIVILEGES;
        exit;
        MYSQL_SCRIPT
        }
        install_php(){
            yum install -y php-gd php-mysql
            systemctl restart httpd
        }
        install_wordpress(){
            cd /tmp
            wget http://wordpress.org/latest.tar.gz
            tar -zxf latest.tar.gz
        }
        config_wordpress(){
            rsync -avP /tmp/wordpress/ /var/www/html/
            mkdir /var/www/html/wp-content/uploads
            chown -R apache:apache /var/www/html/
            cd /var/www/html/
            cp wp-config-sample.php wp-config.php
            sed -i -e "s/database_name_here/$mariadb/g" wp-config.php
            sed -i -e "s/username_here/"$mariauser"/g" wp-config.php
            sed -i -e "s/password_here/"$mariapass"/g" wp-config.php
            # Tidy up
            rmdir -f /tmp/wordpress
            rm -f /tmp/latest.tar.gz
        }
        clear
        printf "=========================================================================\n"
        printf "*********WordPress Installation on CentOS 7 - Edited by Cuo**************\n"
        printf "=========================================================================\n"
        printf "First Step: Creat Database\n"
        printf "==============================\n"
        create_database

        clear
        printf "=========================================================================\n"
        printf "Second Step: Download some PHP modules \n"
        printf "===========================================\n"
        install_php

        clear
        printf "=========================================================================\n"
        printf "Third Step: Download the lastest version of WordPress \n"
        printf "==========================================================\n"
        install_wordpress

        clear
        printf "=========================================================================\n"
        printf "Last Step: Configuration \n"
        printf "=============================\n"
        config_wordpress

        clear
        printf "=========================================================================\n"
        printf "Install successfully , enjoy WordPress! \n"
        printf "=========================================================================\n"
        ```
- **B2 :** Cấp quyền thực thi cho script :
    ```
    # chmod 755 ~/Desktop/wordpress.sh
    ```
- **B3 :** Chạy script :
    ```
    # ./wordpress.sh