# Cài đặt WordPress qua Script
- **B1 :** Download script :
    ```
    # cd ~/Desktop
    # wget https://github.com/QuocCuong97/Public_Zone/blob/master/wordpress.sh
    ```
    - Nội dung script :
        ```
        #!/bin/bash
        clear
        printf "=========================================================================\n"
        printf "WordPress Installation - Edited by Cuo\n"
        printf "=========================================================================\n"
        printf "First Step: Creat Database\n"
        printf "=========================================================================\n"=========================================================================\n"
        echo -n "New MariaDB Name: "
        read mariadb
        echo -n "New MariaDB User: "
        read mariauser
        echo -n "Password: "
        read mariapass
        if [ "$mariadb" != "" ] && [ "$mariauser" != "" ] && [ "$mariapass" != "" ]; then
            break
        fi
        done
        if [ "$mariadb" == "" ] || [ "$mariauser" == "" ] || [ "$mariapass" == "" ]; then
            echo -n "This can't be empty"
        fi
        clear
        printf "=========================================================================\n"
        printf "Second Step: Download some PHP modules \n"
        printf "=========================================================================\n"
        yum install -y php-gd php-mysql
        systemctl restart httpd
        clear
        printf "=========================================================================\n"
        printf "Third Step: Download the lastest version of WordPress \n"
        printf "=========================================================================\n"
        wget http://wordpress.org/latest.tar.gz
        tar -zxf latest.tar.gz
        clear
        printf "=========================================================================\n"
        printf "Last Step: Configuration \n"
        printf "=========================================================================\n"
        rsync -avP ~/wordpress/ /var/www/html/
        mkdir /var/www/html/wp-content/uploads
        chown -R apache:apache /var/www/html/
        cd /var/www/html/
        cp wp-config-sample.php wp-config.php
        sed -i -e "s/database_name_here/$mariadb/g" wp-config.php
        sed -i -e "s/username_here/"$mariauser"/g" wp-config.php
        sed -i -e "s/password_here/"$mariapass"/g" wp-config.php
        rmdir ~/wordpress
        rm -f ~/latest.tar.gz
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