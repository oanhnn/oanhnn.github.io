---
title:    Configure your SSL certificate and HTTPS site to rank A+
layout:   post
category: tutorial-tips
tags:     [skill]
feature:  /assets/img/https.jpg
---

Today, I made a HTTPS site with invalid SSL certificate configuration to rank A+.
And now, i will share my how to do.

<!--more-->

### Make CSR and register SSL certificate

The first, you must make a private key and a certificate signing request (CSR) by openssl.

```shell
$ mkidr ~/ssl-domain.com
$ cd ~/ssl-domain.com/
$ openssl genrsa -out domain_com.key 2048
Generating RSA private key, 2048 bit long modulus
.................................+++
...+++
e is 65537 (0x10001)
$ openssl req -new -sha256 -key domain_com.key -out domain_com.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:VN
State or Province Name (full name) []:Hanoi
Locality Name (eg, city) [Default City]:Hanoi
Organization Name (eg, company) [Default Company Ltd]:LemonPHP Team
Organizational Unit Name (eg, section) []:Development Division
Common Name (eg, your name or your server\'s hostname) []:domain.com
Email Address []:root@domain.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

You can verify your CSR file:

```shell
$ openssl req -noout -text -in domain_com.csr
```

After that, you must submit and register with SSL service provider. I using [Free SSL certificate of COMODO](https://ssl.comodo.com/free-ssl-certificate.php) . I received a compressed file, that includes certificate files.
I uncompress and copy all certificate files to my `ssl-domain.com` directory.

```shell
$ ls -l
-rw-r--r-- 1 oanhnn apache 1521 May  5 04:48 AddTrustExternalCARoot.crt
-rw-r--r-- 1 oanhnn apache 1952 May  5 04:48 COMODORSAAddTrustCA.crt
-rw-r--r-- 1 oanhnn apache 2151 May  5 04:48 COMODORSADomainValidationSecureServerCA.crt
-rw-r--r-- 1 oanhnn apache 1899 May  5 04:48 domain_com.crt
-rw-r--r-- 1 oanhnn apache  997 May  5 04:49 domain_com.csr
-rw-r--r-- 1 oanhnn apache 1708 May  5 04:49 domain_com.key
```

### Configure web server

Generate `dhparam.pem`

```shell
$ openssl dhparam -out dhparam.pem 4096
```

The next, you must concate SSL certificate file for configuring.

```shell
$ cat domain_com.crt COMODORSADomainValidationSecureServerCA.crt COMODORSAAddTrustCA.crt AddTrustExternalCARoot.crt > ssl_bundle.crt
$ cat AddTrustExternalCARoot.crt > trusted.crt
$ sudo mkdir /etc/ngin/cert
$ sudo cp domain_com.key ssl_bundle.crt trusted.crt dhparam.pem /etc/nginx/cert/
```

Go to https://mozilla.github.io/server-side-tls/ssl-config-generator/ and choose your web server.
Copy config and patse to your web server config file. With my case, I using Nginx 1.10.2 with openssl 1.0.1e,

```shell
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    # Redirect all HTTP requests to HTTPS with a 301 Moved Permanently response.
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    # certs sent to the client in SERVER HELLO are concatenated in ssl_certificate
    ssl_certificate /etc/nginx/cert/ssl_bundle.crt;
    ssl_certificate_key /etc/nginx/cert/domain_com.key;
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_session_tickets off;

    # Diffie-Hellman parameter for DHE ciphersuites, recommended 2048 bits
    ssl_dhparam /etc/nginx/cert/dhparam.pem;

    # intermediate configuration. tweak to your needs.
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
    ssl_prefer_server_ciphers on;

    # HSTS (ngx_http_headers_module is required) (15768000 seconds = 6 months)
    add_header Strict-Transport-Security max-age=15768000;

    # OCSP Stapling ---
    # fetch OCSP records from URL in ssl_certificate and cache them
    ssl_stapling on;
    ssl_stapling_verify on;

    ## verify chain of trust of OCSP response using Root CA and Intermediate certs
    ssl_trusted_certificate /etc/nginx/cert/trusted.crt;

    resolver 8.8.8.8 8.8.4.4;

    server_name _;
    root        /var/www/html;
    include     /etc/nginx/default.d/*.conf;
}
```

You can verify NGINX config by command:

```shell
$ sudo nginx -t
```

Reload or restart web server

```shell
$ sudo service reload nginx
```

And the end, you go to https://www.ssllabs.com/ssltest/ to check your SSL certificate rank.

![A HTTPS site with rank A+ SSL certificate](/assets/img/rank-a-plus.png)*A HTTPS site with rank A+ SSL certificate*

You can check on some checker site:

- https://cryptoreport.websecurity.symantec.com/checker/
- https://www.sslshopper.com/ssl-checker.html
- https://www.sslchecker.com/sslchecker

Goodluck!
