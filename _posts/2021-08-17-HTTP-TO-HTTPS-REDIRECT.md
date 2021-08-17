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
