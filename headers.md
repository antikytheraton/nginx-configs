# Set header

```nginx
events {}
http {
    server {
        include mime.types;
        root /sites/demo;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~* \.(css|js|jpg|png)$ {
            access_log off;
            add_header Cache-Control public;
            add_header Pragma public;
            add_header Vary Accept-Encoding;
            expires 1M;
        }
    }
}
```

# Set compression GZIP

```nginx
events {}
http {

    include mime.types;

    gzip on;
    gzip_comp_level 3;

    gzip_types text/css;
    gzip_types text/javascript;

    server {
        root /sites/demo;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~* \.(css|js|jpg|png)$ {
            access_log off;
            add_header Cache-Control public;
            add_header Pragma public;
            add_header Vary Accept-Encoding;
            expires 1M;
        }
    }
}
```
```bash
curl -I -H "Accept-Encoding: gzip, deflate" http://104.248.18.107/style.css


curl http://104.248.18.107/style.css > style.css
curl -H "Accept-Encoding: gzip, deflate" http://104.248.18.107/style.css > style.min.css

ls -l style*
```

# Micro Cache
```nginx
events {}
http {

    include mime.types;

    fastcgi_cache_path /tmp;

    server {
        root /sites/demo;

        location / {
            try_files $uri $uri/ =404;
        }

    }
}
```

