---
title:      Running multiple PHP versions on single Apache instance
layout:     post
category:   tutorial-tips
tags:       [server, php]
feature:    /assets/img/i-love-php.jpg
---

Im more case, you need run multiple applications on single server (single Apache instance) with multiple PHP versions.
What do you do that? In this tutorial, i will present 4 steps to setting up multiple PHP versions on single Apache instance.

<!--more-->

#### Step 1: Build multiple PHP versions from source
Download PHP source from [Github][github-php-src] or [php.net][php-home-page].
Build from downloaded source follow below command:

```shell
$ wget http://us1.php.net/get/php-5.3.29.tar.gz/from/this/mirror
$ tar -xvzf mirror
$ cd php-5.3.29
$ ./configure --prefix=/usr/local/php53 \
    --with-config-file-path=/etc/php53 \
    --with-config-file-scan-dir=/etc/php53/php.d \
    --with-libdir=lib64 \
    --enable-mbstring \
    --disable-debug \
    --disable-rpath \
    --enable-mbstring \
    --enable-mbregex \
    --enable-phar \
    --enable-posix \
    --enable-soap \
    --enable-sockets \
    --enable-sysvmsg \
    --enable-sysvsem \
    --enable-sysvshm \
    --enable-zip \
    --enable-inline-optimization \
    --enable-intl \
    --with-icu-dir=/usr \
    --with-curl=/usr/bin \
    --with-gd \
    --with-jpeg-dir=/usr \
    --with-png-dir=shared,/usr \
    --with-xpm-dir=/usr \
    --with-freetype-dir=/usr \
    --with-bz2=/usr \
    --with-gettext \
    --with-iconv-dir=/usr \
    --with-mcrypt=/usr \
    --with-mhash \
    --with-zlib-dir=/usr \
    --with-regex=php \
    --with-pcre-regex \
    --with-openssl \
    --with-openssl-dir=/usr/bin \
    --with-mysql-sock=/var/run/mysqld/mysqld.sock \
    --with-mysqli=mysqlnd \
    --with-sqlite3=/usr \
    --with-pdo-mysql=mysqlnd \
    --with-pdo-sqlite=/usr \
    --enable-cgi
$ make
$ make install
$ mkdir /etc/php53
$ cp php.ini-production /etc/php53/php.ini
```

#### Step 2: Install Apache and `mod_fastcgi`
You can using google to find: how to install it

#### Step 3: Config PHP handler
Making cgi script for each installed PHP versions. This is content of one script (`/var/www/cgi-bin/php53.fcgi`)

```shell
#!/bin/bash
PHPRC="/etc/php53/php.ini"
PHP_CGI=/usr/local/php53/bin/php-cgi
PHP_FCGI_CHILDREN=8
PHP_FCGI_MAX_REQUESTS=3000
export PHPRC
export PHP_FCGI_CHILDREN
export PHP_FCGI_MAX_REQUESTS
exec $PHP_CGI
```

Modified and add below block to Apache config (recommend add to `/etc/httpd/conf.d/fcgi.conf`).

```conf
# /etc/httpd/conf.d/fcgi.conf
LoadModule fcgid_module modules/mod_fcgid.so

<IfModule mod_fcgid.c>
    # Use FastCGI to process .fcg .fcgi & .fpl scripts
    AddHandler fcgid-script fcg fcgi fpl

    # PHP 5.3
    Action application/x-httpd-php53 /cgi-bin/php53.fcgi
    AddType application/x-httpd-php53 .php53

    # PHP 5.4
    Action application/x-httpd-php54 /cgi-bin/php54.fcgi
    AddType application/x-httpd-php54 .php54

    # PHP 5.5 default
    Action application/x-httpd-php55 /cgi-bin/php55.fcgi
    AddType application/x-httpd-php55 .php55

    # Sane place to put sockets and shared memory file
    FcgidIPCDir /var/run/mod_fcgid
    FcgidProcessTableFile /var/run/mod_fcgid/fcgid_shm
</IfModule>
```

#### Step 4: Config virtual host

```conf
<VirtualHost *:80>
    ServerName your-domain.dev
    DocumentRoot /var/www/apps/your-webroot/
    ErrorLog logs/error-your-app.log
    CustomLog logs/access-your-app.log combined
    <Directory /var/www/apps/your-webroot/>
        Options FollowSymLinks +ExecCGI
        AllowOverride All
        Order allow,deny
        Allow from all
        <IfModule mod_fcgid.c>
            <FilesMatch \.php$>
                SetHandler application/x-httpd-php53
            </FilesMatch>
        </IfModule>
    </Directory>
</VirtualHost>
```

[github-php-src]: https://github.com/php/php-src
[php-home-page]:  http://php.net/downloads.php