# Hardening Nginx

# Setting up
```bash
apt-get update
apt-get upgrade

nginx -V
curl -Ik https://104.248.18.107/thumb.png
```

```http
HTTP/2 200
server: nginx/1.15.9
date: Tue, 26 Mar 2019 20:23:04 GMT
content-type: image/png
content-length: 12627
last-modified: Thu, 21 Mar 2019 16:28:48 GMT
etag: "5c93bbc0-3153"
strict-transport-security: max-age=31536000
accept-ranges: bytes
```

## Hide Nginx flags

```nginx
user www-data;

worker_processes auto;

events {
    worker_connections 1024;
}

http {

    include mime.types;

    # Hide nginx version info
    server_tokens off;

    server {
        listen 80;
        server_name 167.99.93.26;
        return 301 https://$host$request_uri;
  }

  server {

    listen 443 ssl http2;
    server_name 167.99.93.26;

    root /sites/demo;

    index index.html;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";

    ssl_certificate /etc/nginx/ssl/self.crt;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DH+3DES:!ADH:!AECDH:!MD5;

    ssl_dhparam /etc/nginx/ssl/dhparam.pem;

    add_header Strict-Transport-Security "max-age=31536000" always;

    ssl_session_cache shared:SSL:40m;
    ssl_session_timeout 4h;
    ssl_session_tickets on;

    location / {
        try_files $uri $uri/ =404;
    }
  }
}
```


```bash
curl -Ik https://104.248.18.107/thumb.png
```

```http
HTTP/2 200
server: nginx
date: Tue, 26 Mar 2019 20:25:13 GMT
content-type: image/png
content-length: 12627
last-modified: Thu, 21 Mar 2019 16:28:48 GMT
etag: "5c93bbc0-3153"
strict-transport-security: max-age=31536000
accept-ranges: bytes

```


## Remove weak modules
```bash
cd nginx
./configure --help | grep without

nginx -V

./configure \
--sbin-path=/usr/bin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-pcre \
--pid-path=/var/run/nginx.pid \
--with-http_ssl_module \
--with-http_image_filter_module=dynamic \
--modules-path=/etc/nginx/modules \
--with-http_v2_module \
--without-http_autoindex_module

make -j$(nproc)

sudo make install

sudo systemctl reload nginx
```