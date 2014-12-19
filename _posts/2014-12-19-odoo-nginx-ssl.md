---
layout: post
title: Odoo with nginx ssl 
comments: true
permalink: "odoo-nginx-ssl"
---
### Nginx provides ssl to Odoo website
__Installation__  

```bash
sudo apt-get update && sudo apt-get install nginx

#verify nginx process is functioning
netstat -tupln |grep :443

#Save and close the file. Reload nginx:
/usr/local/nginx/sbin/nginx -t 
/usr/local/nginx/sbin/nginx -s reload
```

__Configuration__

```bash
#A basic nginx site configuration (/etc/nginx/sites-enabled/odoo.com) could be:

server {
    listen      443 default;
    server_name www.yourSite.com;

    access_log  /var/log/nginx/oddo.access.log;
    error_log   /var/log/nginx/oddo.error.log;

    ssl on;
    ssl_certificate     /etc/nginx/ssl/server.crt; #digital certification
    ssl_certificate_key /etc/nginx/ssl/server.key; #private key
    keepalive_timeout   60;

    ssl_ciphers             HIGH:!ADH:!MD5;
    ssl_protocols           SSLv3 TLSv1;
    ssl_prefer_server_ciphers on;

    proxy_buffers 16 64k;
    proxy_buffer_size 128k;

    location / {
        proxy_pass  http://odoo.host:8069/;
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        proxy_redirect off;

        proxy_set_header    Host            $host;
        proxy_set_header    X-Real-IP       $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto https;
    }

    location ~* /web/static/ {
        proxy_cache_valid 200 60m;
        proxy_buffering on;
        expires 864000;
        proxy_pass http://odoo.host:8069;
    }
}

server {
    listen      80;
    server_name www.yourSite.com;

    add_header Strict-Transport-Security max-age=2592000;
    rewrite ^/.*$ https://$host$request_uri? permanent;
}
```

##Bootup with startssl

StartCom®为一家提供数字证书认证的公司,这次建立odoo网站正好可以利用它的一年期免费证书，这个免费证书是一类的，在主流的浏览器上都可以被识别，
唯独稍显麻烦的是每过一个月需要对使用了该数字证书的域名进行再验证。最终得到主要的文件就是秘钥和域名证书，需提供给nginx：

```bash
1.private key
#decrypt the private key,sslo.key file
#需输入私钥的密码, 生成解密后的私钥内容,可以避免每次启动nginx时输入密码
openssl rsa -in ssl.key -out sslo.key
chmod 600 sslo.key （Protect your key from prying eyes）

2.合并证书配置文件
和Apache配置不同，Nginx需要将服务器证书和ca证书链合并到一个文件中，作为 ssl_certificate 配置的内容。
例如，按照证书链从下向上的顺序，有三个证书：
  1)ssl.crt（自己域名的服务器证书，从startssl上申请下来的）
  2)sub.class1.server.ca.pem（startssl 的一类证书）
  3)ca.pem（startssl 的根证书）
#Fetch the Root CA and Class 1 Intermediate Server CA certificates:
wget http://www.startssl.com/certs/ca.pem
wget http://www.startssl.com/certs/sub.class1.server.ca.pem

#Create a unified certificate from your certificate and the CA certificates:
cat ssl.crt sub.class1.server.ca.pem ca.pem > /etc/nginx/conf/ssl-unified.crt

#Configure your nginx server to use the new key and certificate (in the global settings or a server section):
ssl on;
ssl_certificate /etc/nginx/conf/ssl-unified.crt;
ssl_certificate_key /etc/nginx/conf/ssl.key;
#Tell nginx to reload its configuration:
killall -HUP nginx
```

## Trouble shooting 
1.default port 80 is using ,remove the 'sites-enable/default' file  
2.[firefox warns"untrusted authority" when connecting to my website]
(http://www.startssl.com/?app=25#31)  

##reference
[start ssl证书申请](http://www.live-in.org/archives/1296.html)  
[ssl证书与Https应用部署小结 ](http://han.guokai.blog.163.com/blog/static/136718271201211631456811/)  
[setup-nginx-ssl-proxy](http://www.cyberciti.biz/faq/howto-linux-unix-setup-nginx-ssl-proxy/)  
[nginx Reverse Proxy](http://nginx.com/resources/admin-guide/reverse-proxy/)  