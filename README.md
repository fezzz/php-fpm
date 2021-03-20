# php-fpm 8.0x

This php-fpm container is meant to be used together with a webserver and database container.
It serves the service internally on port 9000.
Be sure to create a mail.rc config file on the host and mount it into the php-fpm container, so that you are able to send emails from within.

I use the following sample configuration for nextcloud in connection with a nginx and mariadb container:

~~~
web:
 image: nginx:latest
 ports:
        - "80:80"
        - "443:443"
 volumes:
        - /HOST/NEXTCLOUD_DATA_DIR:/CONTAINER/cloud
        - /HOST/webroot:/var/www/htdocs
        - /HOST/serverconfig/nginx:/etc/nginx
        - /HOST/logs/nginx:/var/log/nginx
        - /HOST/DHPARAMS_FILE:/etc/ssl/nginx:ro
        - /HOST/SSLCERTIFICATES:/etc/letsencrypt:ro
 links:
        - php
        - mysql
 restart: always

php:
 image: fezzz/php-fpm
 volumes:
        - /HOST/serverconfig/mailx/mail.rc:/etc/mail.rc
        - /HOST/NEXTCLOUD_DATA_DIR:/CONTAINER/cloud
        - /HOST/webroot:/var/www/htdocs
 links:
        - mysql
 restart: always

mysql:
 image: mariadb:latest
 volumes:
        - /HOST/DATABASES:/var/lib/mysql
 restart: always
~~~
