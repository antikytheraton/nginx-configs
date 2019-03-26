# Rate limiting

```bash
sudo apt-get install siege
siege -v -r 2 -c 5 https://104.248.18.107/thumb.png
curl -Ik https://104.248.18.107/thumb.png
```


```nginx
user www-data;
worker_processes auto;
events {
  worker_connections 1024;
}
http {

  include mime.types;

  # Define rate limiting zone / $server_name / $binary_remote_addr / $request_uri
  limit_req_zone $request_uri zone=MYZONE:10m rate=1r/s;

  server {
    listen 80;
    server_name 104.248.18.107;
    return 301 https://$host$request_uri;
  }

  server {

    listen 443 ssl http2;
    server_name 104.248.18.107;

    root /sites/demo;
    index index.html;

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
      limit_req zone=MYZONE burst=5 nodelay;
      try_files $uri $uri/ =404;
    }
  }
}
```