# Serving a static site

### Edit /etc/nginx/nginx.conf
```nginx
events {}

http {
    # mime.types support
    include mime.types;

    server {

        listen 80;
        server_name 46.101.201.248;

        root /sites/demo;

        # Prefix match
        location /greet {
            return 200 'Hello from NGINX "/greet"'
        }

        # Exact match
        location = /greet {
            return 200 'Hello from NGINX "/greet" - EXACT MATCH';
        }

        # REGEX match - case sensitive
        location ~ /greet[0-9] {
            return 200 'Hello from NGINX "/greet" - REGEX MATCH';
        }

        # REGEX match - case insensitive
        location ~* /greet[0-9] {
            return 200 'Hello from NGINX "/greet" - REGEX MATCH INSENSITIVE';
        }
        # Preferential uri
        location ^~ /greet {
            return 200 'Hello from NGINX "/greet"'
        }
    }
}
```

### Test CSS
```bash
curl -I http://46.101.201.248/style.css
```

## Variables

```nginx
events {}

http {
    include mime.types:

    server {

        listen 80;
        server_name 46.101.201.248;

        root /sites/demo;

        location /inspect {
            return 200 "$host\n$uri\n$args";
        }
    }
}
```