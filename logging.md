# Special logs

```nginx
events {}
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
    }
}
```