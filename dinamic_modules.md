# Add dynamic modules

```
nginx -V

nginx version: nginx/1.15.9
built by gcc 8.2.0 (Ubuntu 8.2.0-7ubuntu1) 
built with OpenSSL 1.1.1  11 Sep 2018
TLS SNI support enabled
configure arguments: --sbin-path=/usr/bin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-pcre --pid-path=/var/run/nginx.pid --with-http_ssl_module
```

```bash
sudo apt-get install libgd-dev

./configure \
--sbin-path=/usr/bin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-pcre \
--pid-path=/var/run/nginx.pid \
--with-http_ssl_module \
--with-http_image_filter_module=dynamic \
--modules-path=/etc/nginx/modules

make

make install

nginx -V

systemctl reload nginx
```


```nginx
worker_processes auto;

load_module /etc/nginxmodules/ngx_http_image_filter_module.so;

events {
    worker_connections 1024;
}
http {
    include mime.types;

    server {
        listen 80;
        server_name 104.248.18.107;

        root /sites/demo;

        location /secure {
            access_log  /var/log/nginx/secure.access.log;
            return 200 "Welcome to secure area.";
        }

        location = /thumb.png {
            image_filter rotate 180;
        }
    }
}
```