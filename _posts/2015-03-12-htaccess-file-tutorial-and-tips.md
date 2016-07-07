---
title:      Htaccess file tutorial and tips
layout:     post
category:   tutorial-tips
tags:       [web, apache]
---

With `.htaccess` file, you can easily configure and redirect Apache Web Server file system.
This post will explain you how to create friendly URLs, sub domain directory re-directions and many more.

<!--more-->

> Note: `.htaccess` file will be in hidden format, please change your folder and file settings to view this file.

### How to create a `.htaccess` file?

Open any text editor application and file save as with `.htaccess` name and
enable mod_rewrite extension in php.ini file in Apache Web Server configurations.


### Disable directory Listing

If you want to disable folder files listing, include following code.

```conf
Options All -Indexes
```

### Error Pages

Here error page is redirecting to error.html.

```
ErrorDocument 400 http://www.yourwebsite.com/error.html
ErrorDocument 401 http://www.yourwebsite.com/error.html
ErrorDocument 404 http://www.yourwebsite.com/error.html
ErrorDocument 500 "Sorry, our script crashed. Oh dear"
```

### RewriteEngine On

RewriteEngine On it is turn on Rewrite Rules in Apache Server.
if you want to turn off, just change the value to off.

```conf
RewriteEngine on
```

### Hide `.php` extension with URL Rewriting

For example if we want to project like Twitter API URLs (Note: Twitter API Developed in Ruby on Rails)

```conf
RewriteEngine on
RewriteRule ^(.*)\$ $1.php
```

We can Rewrite `index.php` into `index.html`,`index.asp`,`index.sri` also …
Below code for `index.php` to `index.html`

```conf
RewriteEngine on
RewriteRule ^(.*)\.html$ $1.php
```

If you want `.asp` extension just replace `html` to `asp`

### Redirecting www URL to non www URL

If you type _`www.yourwebsite.com`_ in browser it will be redirected to _`yourwebsite.com`_ .   
Add this Following Code:

```conf
RewriteEngine On
RewriteCond %{HTTP_HOST} ^www.yourwebsite.com
RewriteRule (.*) http://yourwebsite.com/$1 [R=301,L]
```

Conversely, redirecting _`yourwebsite.com`_ to _`www.yourwebsite.com`_

```conf
RewriteEngine On
RewriteCond %{HTTP_HOST} ^yourwebsite.com
RewriteRule (.*) http://www.yourwebsite.com/$1 [R=301,L]
```

### Sub Domain Redirection   

Sub domain redirection mapping to folder.   
Here _`www.yourwebsite.com`_ is connecting to `website_folder` folder.   
And _`subdomain.yourwebsite.com`_ is connecting to `subdomain_folder` folder.

```conf
RewriteEngine On
RewriteCond %{HTTP_HOST} ^www\.yourwebsite\.com$
RewriteCond %{REQUEST_URI} !^/website_folder/
RewriteRule (.*) /website_folder/$1

RewriteCond %{HTTP_HOST} ^subdomain\.yourwebsite\.com$
RewriteCond %{REQUEST_URI} !^/subdomain_folder/
RewriteRule (.*) /subdomain_folder/$1
```

### Friendly URL with parameters
Original URL : _`yourwebsite.com/followers.php?id=abc123`_   
to   
Rewriting URL : _`yourwebsite.com/abc123/followers`_   

```conf
RewriteEngine On
RewriteRule ^([a-zA-Z0-9_-]+)/followers$ followers.php?id=$1
RewriteRule ^([a-zA-Z0-9_-]+)/followers/$ following.php?id=$1
```

### Htaccess File Inside The Folder

Original URL : _`www.yourwebsite.com/blog/index.php?id=abc123`_   
to   
Friendly URL : _`blog.yourwebsite.com/abc123`_   

First `.htaccess` file   
This code redirects sub domain blog.yourwebsite.com pointing to blog folder.   

```conf
RewriteEngine On
RewriteCond %{HTTP_HOST} ^blog\.yourwebsite\.com$
RewriteCond %{REQUEST_URI} !^/blog/
RewriteRule (.*) /blog/$1
```

Second `.htaccess` file   
This file inside the blog folder. Contains single parameter URL rewriting code.   

```conf
RewriteEngine On
RewriteBase /blog/
RewriteEngine On
RewriteRule ^([a-zA-Z0-9_-]+)$ index.php?id=$1
RewriteRule ^([a-zA-Z0-9_-]+)/$ index.php?id=$1
```
