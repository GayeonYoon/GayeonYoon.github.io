---
layout: post
title: "[Codeigniter] .htaccess 보안취약점 Header set 설정"
date: 2021-08-16
excerpt: ".htaccess 보안취약점 Header set 설정"
tags: [PHP 7, Codeigniter, htaccess, Xss, Header]
comments: false
ci: true
feature: /assets/img/php_logo.jpg
---

# 1. www/.htaccess 파일 생성

{% highlight apache %}
<IfModule mod_rewrite.c>
 RewriteEngine On
 RewriteBase /
 RewriteCond $1 !^(index\.php|images|captcha|data|include|upload|robots\.txt)
 RewriteCond %{REQUEST_FILENAME} !-f
 RewriteCond %{REQUEST_FILENAME} !-d
 RewriteRule ^(.*)$ /index.php/$1 
</IfModule>
# php -- BEGIN cPanel-generated handler, do not edit
# Set the “ea-php74” package as the default “PHP” programming language.
<IfModule mime_module>
  AddHandler application/x-httpd-ea-php74 .php .php7 .phtml
</IfModule>
# php -- END cPanel-generated handler, do not edit
Options -Indexes

<FilesMatch "\.(html|htm|js|php)$">
  <IfModule mod_headers.c>
	Header set Content-Security-Policy  "script-src * 'self' 'unsafe-inline' 'unsafe-eval';  object-src; frame-ancestors 'self';   " 
	Header set X-Content-Type-Options "nosniff"
	Header set X-XSS-Protection "1;mode=block"
	Header set Strict-Transport-Security "max-age=63072000; includeSubDomains;preload;"
	Header set Cache-Control "no-store"
	Header set Pragma "no-cache"
  </IfModule>
</FilesMatch>
{% endhighlight %}
