---
title: How to force HTTPS behind AWS ELB
layout:   post
category: tutorial-tips
tags:     [skill]
feature:  /assets/img/aws-elb.png
---

## Problem
In the most common case, when running your web app behind Elastic Load Balancing (ELB)
of Amazon Web Service (AWS) with on both HTTP and HTTPS requests.
The ports configuration is `80 => 80` and `443 => 80` . Sometimes, you may want to rewrite
all HTTP requests to HTTPS.

<!--more-->

![The most common ELB configuration](/assets/img/https-aws-elb.png)*The most common
ELB configuration with both HTTP and HTTPS*

## Solution
The ELB supports a HTTP header called `X-FORWARDED-PROTO`. All the HTTPS requests
going through the ELB will have the value of `X-FORWARDED-PROTO` equal to `https`.
For the HTTP requests, you can force HTTPS by adding a simple rewrite rule follows this header.

## How to?
In your web server, setting up a rewrite rule:

1. #### Using NGINX ####

   In your nginx site config file check if the value of `X_FORWARDED_PROTO` is `https`,
   if not, rewrite it:

   ```conf
   server {
       listen 80;
       #....
       location / {
           if ($http_x_forwarded_proto != 'https') {
               return 301 https://$server_name$request_uri;
           }
           try_files $uri $uri/ /index.php?$args;
       #....
       }
   }
   ```

2. #### Using Apache ####

   Same goes for Apache, add this rewrite rule to your site’s config file:

   ```conf
   <VirtualHost *:80>
       #...
       RewriteEngine On
       RewriteCond %{HTTP:X-Forwarded-Proto} !https
       RewriteRule ^(.*)$ https://%{SERVER_NAME}%{REQUEST_URI} [NE]
       #...
   </VirtualHost>
   ```

3. #### Using IIS ####

   Install IIS Url-Rewrite module, using the configuration GUI add these settings:

   ```xml
   <rewrite xdt:Transform="Insert">
     <rules>
       <rule name="HTTPS rewrite behind ELB rule" stopProcessing="true">
         <match url="^(.*)$" ignoreCase="false" />
         <conditions>
           <add input="{HTTP_X_FORWARDED_PROTO}" pattern="^http$" ignoreCase="false" />
         </conditions>
         <action type="Redirect" redirectType="Found" url="https://{SERVER_NAME}{URL}" />
       </rule>
     </rules>
   </rewrite>
   ```

## One more thing
Sometimes, your ELB health check request is HTTP (port 80), you must exclude it
from rewrite rule or make other port for health check request. Eg with NGINX:

```conf
server {
    listen 80 default_server;
    server_name "";
    location /health {
        access_log off;
        return  200;
    }
}
server {
    listen 80;
    server_name app.yourdomain.com;
    #....
    location / {
        if ($http_x_forwarded_proto != 'https') {
            return 301 https://$server_name$request_uri;
        }
        try_files $uri $uri/ /index.php?$args;
    }
    #....
}
```

Goodluck!
