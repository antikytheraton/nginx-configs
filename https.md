# HTTPS (SSL)
```bash
openssl req -x509 -days 10 -nodes -newkey rsa:2048 -keyout /etc/nginx/ssl/self.key -out /

etc/nginx/ssl/self.crt

openssl dhparam 2048 -out /etc/nginx/ssl/dhparam.pem
```

```nginx
events {}
http {

    include mime.types;

    # Redirect all trafic to HTTPS
    server {
        listen 80;
        server_name 104.248.18.107;
        return 301 https://$host$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name 104.248.18.107;

        root /sites/demo;

        ssl_certificate /etc/nginx/ssl/self.crt;
        ssl_certificate_key /etc/nginx/ssl/self.key;

        # Disable SSL
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

        # Optimize cipher suit
        ssl_prefer_server_ciphers on;
        ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DH+3DES:!ADH:!AECDH:!MD5;

        # Enable Diffie-Hellman params
        ssl_dhparam /etc/nginx/ssl/dhparam.pem;

        # Enable HSTS
        add_header Strict-Transport-Security "max-age=31536000" always;

        # SSL Sessions
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
curl -Ik http://104.248.18.107
```

