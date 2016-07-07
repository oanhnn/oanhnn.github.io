---
title:      Getting real IP while using nginx working behind AWS ELB
layout:     post
category:   tutorial-tips
tags:       [server, deployment]
feature:    /assets/img/aws-elb.png
---

When placing nginx web servers behind Elastic Load Balancing (ELB) of Amazon Web Service (AWS),
the client IP address from the browser is replaced with the IP address of the load-balancer.
This makes difficult to analysis the location of IP.

<!--more-->

Here’s how you can fix it and capture the real IP.

## Prerequisite

Make sure you have http_realip_module with nginx . You can check it this way:

```shell
$ nginx -V
```

## Config module

Open `/etc/nginx/nginx.conf` file and add following parameters inside http block.

```nginx
# vi /etc/nginx/nginx.conf

http {
    # ...

    ##
    # Real IP
    ##
    real_ip_header X-Forwarded-For;
    set_real_ip_from 10.0.0.0/8; # <- subnet IPs or Elastic Load Balance IP

    # ...
}
```

After this reload the nginx and check the access logs.

> Note : If the traffic  only coming through elastic load balancer,
> then set IP address assigned to elastic load balancer.
> Suppose IP address for elastic load balance  is 1.2.3.4, then it should look like
>
> ```nginx
> # vi /etc/nginx/nginx.conf
>
> http {
>     # ...
>
>     ##
>     # Real IP
>     ##
>     real_ip_header X-Forwarded-For;
>     set_real_ip_from 1.2.3.4; # <- ELB IP
>
>     # ...
> }
> ```

## Protecting your site

Open your vhost config file (Eg: `/etc/nginx/site-enabled/default.conf`) and add following parameters inside http block.

```nginx
# vi /etc/nginx/site-enabled/default.conf

server {
    # ...
    location ~ ^/(admin|protected) {
        allow 127.0.0.1;
        allow 54.xx.xx.xx; # <- your IP
        deny all;

        # ...
    }
    # ...
}
```

Successfully! Goodluck !
