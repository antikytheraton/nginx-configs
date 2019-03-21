# Installing Nginx

### Connect to host
```bash
$ ssh root@46.101.201.248
```

### Some hints

* Remove old known hosts
```bash
$ ssh-keygen -R {host_ip}
```

### Downloading Nginx
```bash
wget https://nginx.org/download/nginx-1.15.9.tar.gz
tar -zxvf nginx-1.15.9.tar.gz
cd nginx-1.15.9
```

### INstall dependencies
```bash
apt-get install build-essential
apt-get install libpcre3 libpcre3-dev zlib1g zlib1g-dev libssl-dev
```

### Configure and install Nginx
```bash
./configure \
--sbin-path=/usr/bin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-pcre \
--pid-path=/var/run/nginx.pid \
--with-http_ssl_module

make

make install

nginx
nginx -s stop
```

### Configure Systemd service
```bash
touch /lib/systemd/system/nginx.service
vim /lib/systemd/system/nginx.service
```

```systemd
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/bin/nginx -t
ExecStart=/usr/bin/nginx
ExecReload=/usr/bin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

```bash
systemctl start nginx.service
systemctl enable nginx.service
```

