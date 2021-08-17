---
layout: post
title: "[Codeigniter] 다중 DB 연결하기"
date: 2021-08-06
excerpt: "다중 DB 연결하기"
tags: [PHP 7, Codeigniter, DB, MySQL]
comments: false
ci: true
feature: /assets/img/php_logo.jpg
---

# 1. www/.htaccess 파일 생성

<IfModule mod_rewrite.c>
 RewriteEngine On
 RewriteBase /
 RewriteCond %{SERVER_PORT} 80  
 RewriteRule ^(.*)$ https://google.com/$1 [R,L] # domain 입력!!
 RewriteCond $1 !^(index\.php|images|captcha|data|include|upload|robots\.txt)
 RewriteCond %{REQUEST_FILENAME} !-f
 RewriteCond %{REQUEST_FILENAME} !-d
 RewriteRule ^(.*)$ /index.php/$1 
</IfModule> 
