# Basic Auth

```bash
sudo apt-get install apache2-utils
or
sudo yum install httpd-tools


htpasswd -c /etc/nginx/.htpasswd user1
```

```nginx
user www-data;

worker_processes auto;

events {
    worker_connections 1024;
}

http {

    include mime.types;

    # Define limit zone
    limit_req_zone $request_uri zone=MYZONE:10m rate=1r/s;

    # Redirect all traffic to HTTPS
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
        auth_basic "Secure Area";
        auth_basic_user_file /etc/nginx/.htpasswd;
        try_files $uri $uri/ =404;
    }
  }
}
```
