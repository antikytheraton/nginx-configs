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

## If statements

```nginx
events {}

http {
    include mime.types;

    server {
        listen 80;
        server_name 104.248.18.107;
        root /sites/demo;

        if ( $arg_apikey != 1234 ) {
            return 401 "Incorrect API KEY";
        }
        location /inspect {
            return 200 "Name: $arg_name";
        }
    }
}
```

http://104.248.18.107/inspect?apikey=1234&name=Aaron


# Is weekend?
```nginx
events {}
http {
    include mime.types;
    server {
        listen 80;
        server_name 104.248.18.107;
        root /sites/demo;

        set $weekend 'No';

        if ($date_local ~ 'Saturday|Sunday') {
            set $weekend 'Yes';
        }

        location /is_weekend {
            return 200 $weekend;
        }
    }
}
```


# Redirect

```nginx
events {}
http {

    include mime.types;
    server{
        listen 80;
        server_name 104.248.18.107;

        root /sites/demo;

        location /logo {
            return 307 /thumb.png;
        }
    }
}
```

# Rewrites

```nginx
events {}
http {

    include mime.types;
    server{
        listen 80;
        server_name 104.248.18.107;

        root /sites/demo;

        rewrite ^/user/(\w+) /greet/$1;

        location /greet {
            return 200 "Hello User";
        }

        location /greet/aaron {
            return 200 "Hello Aaron";
        }
    }
}
```

# Try files

```nginx
events {}
http {

    include mime.types;
    server{
        listen 80;
        server_name 104.248.18.107;

        root /sites/demo;

        #try_files /thumb.png /;
        try_files $uri /cat.png /greet;

        location /greet {
            return 200 "Hello User";
        }
    }
}
```