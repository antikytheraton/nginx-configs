# HTTP2


## Adding HTTP V2 module
```bash
nginx -V

--sbin-path=/usr/bin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-pcre --pid-path=/var/run/nginx.pid --with-http_ssl_module --with-http_image_filter_module=dynamic --modules-path=/etc/nginx/modules

./configure --help | grep http_v2

./configure \
--sbin-path=/usr/bin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-pcre \
--pid-path=/var/run/nginx.pid \
--with-http_ssl_module \
--with-http_image_filter_module=dynamic  \
--modules-path=/etc/nginx/modules \
--with-http_v2_module

make

make install

systemctl restart nginx
```

# SSL certificates own generated
```bash
mkdir /etc/nginx/ssl

openssl req -x509 -days 10 -nodes -newkey rsa:2048 -keyout /etc/nginx/ssl/self.key -out /etc/nginx/ssl/self.crt
```

```nginx
events {}
http {

    include mime.types;

    server {

        listen 443 ssl http2;
        server_name 104.248.18.107;

        root /sites/demo;

        ssl_certificate /etc/nginx/ssl/self.crt;
        ssl_certificate_key /etc/nginx/ssl/self.key;

        location / {
            try_files $uri $uri/ =404;
        }

    }
}
```

## Server push

[nghttp2](https://www.nghttp2.org)

```bash
apt-get install nghttp2-client

nghttp -nys https://104.248.18.107/index.html

nghttp -nysa https://104.248.18.107/index.html
```

```nginx
events {}
http {

    include mime.types;

    server {

        listen 443 ssl http2;
        server_name 104.248.18.107;

        root /sites/demo;

        ssl_certificate /etc/nginx/ssl/self.crt;
        ssl_certificate_key /etc/nginx/ssl/self.key;

        location / {
            try_files $uri $uri/ =404;
        }

        location = /index.html {
            http2_push /style.css;
            http2_push /thumb.png;
        }

    }
}
```