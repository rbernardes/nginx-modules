
# Ubuntu 18.04 + nginx + modules (headers more + geoip2 + pagespeed + flv + brotli + passenger) + php7.2-fpm
![nginx](https://github.com/rbernardes/nginx-modules/blob/master/nginx_logo.png?raw=true)

### Update your Ubuntu with these repositories:
```
vim /etc/apt/sources.list
#### nginx ####
deb http://nginx.org/packages/ubuntu/ bionic nginx
deb-src http://nginx.org/packages/ubuntu/ bionic nginx

#### php ####
deb http://ppa.launchpad.net/ondrej/php/ubuntu bionic main

#### MaxMind ####
deb http://ppa.launchpad.net/maxmind/ppa/ubuntu bionic main
#deb-src http://ppa.launchpad.net/maxmind/ppa/ubuntu bionic main

Save the file and then run

curl -L https://nginx.org/keys/nginx_signing.key | sudo apt-key add -
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 4F4EA0AAE5267A6C 561F9B9CAC40B2F7 DE1997DCDE742AFA
sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger bionic main > /etc/apt/sources.list.d/passenger.list'

apt update && apt dist-upgrade
```

### Install required packages:
```
apt-get install uuid-dev dpkg-dev build-essential zlib1g-dev libpcre3 libpcre3-dev unzip git passenger libmaxminddb0 libmaxminddb-dev mmdb-bin libgeoip-dev
```

### Download source and build dependencies:
```
cd /usr/local/src
sudo apt source nginx
apt build-dep nginx -y
```

### Download modules:
```
cd /usr/local/src
git clone --recursive https://github.com/google/ngx_brotli.git
git clone --recursive https://github.com/winshining/nginx-http-flv-module.git

git clone https://github.com/leev/ngx_http_geoip2_module.git
git clone https://github.com/openresty/headers-more-nginx-module.git

wget https://github.com/apache/incubator-pagespeed-ngx/archive/v1.13.35.2-stable.tar.gz
wget https://www.modpagespeed.com/release_archive/1.13.35.2/psol-1.13.35.2-x64.tar.gz

tar xpf psol-1.13.35.2-x64.tar.gz
tar xvzf v1.13.35.2-stable.tar.gz
mv incubator-pagespeed-ngx-1.13.35.2-stable/ pagespeed-ngx-1.13.35.2-stable
cd pagespeed-ngx-1.13.35.2-stable/
mv ../psol/ .
cd ..
rm psol-1.13.35.2-x64.tar.gz v1.13.35.2-stable.tar.gz
```

### Change the "rules" file (attached):
```
Path: /usr/local/src/nginx-1.14.2/debian
```

### Build packages:
```
/usr/local/src/nginx-1.14.2/
dpkg-buildpackage -b -uc -us
```

> Use the available Release binaries? [Y/n] Y [ENTER]

### Install nginx:
```
cd /usr/local/src
root@u1804atom:/usr/local/src# ls -al
total 26844
drwxr-xr-x  8 root root     4096 Mar 14 21:23 .
drwxr-xr-x 10 root root     4096 Jul 25  2018 ..
drwxr-xr-x  6 root root     4096 Mar 14 20:59 headers-more-nginx-module
drwxr-xr-x 10 root root     4096 Mar 14 21:22 nginx-1.14.2
-rw-r--r--  1 root root     6308 Mar 14 21:23 nginx_1.14.2-1~bionic_amd64.buildinfo
-rw-r--r--  1 root root     1312 Mar 14 21:23 nginx_1.14.2-1~bionic_amd64.changes
-rw-r--r--  1 root root  9503536 Mar 14 21:23 **nginx_1.14.2-1~bionic_amd64.deb**
-rw-r--r--  1 root root   111508 Dec  4 15:30 nginx_1.14.2-1~bionic.debian.tar.xz
-rw-r--r--  1 root root     1510 Dec  4 15:30 nginx_1.14.2-1~bionic.dsc
-rw-r--r--  1 root root  1015384 Dec  4 15:30 nginx_1.14.2.orig.tar.gz
-rw-r--r--  1 root root 16797832 Mar 14 21:23 **nginx-dbg_1.14.2-1~bionic_amd64.deb**
drwxr-xr-x  9 root root     4096 Mar 14 20:57 nginx-http-flv-module
drwxr-xr-x  5 root root     4096 Mar 14 20:56 ngx_brotli
drwxr-xr-x  3 root root     4096 Mar 14 20:59 ngx_http_geoip2_module
drwxrwxr-x  6 root root     4096 Mar 14 20:58 pagespeed-ngx-1.13.35.2-stable

root@u1804atom:/usr/local/src# dpkg -i *.deb
Selecting previously unselected package nginx.
(Reading database ... 111243 files and directories currently installed.)
Preparing to unpack nginx_1.14.2-1~bionic_amd64.deb ...
----------------------------------------------------------------------

Thanks for using nginx!

Please find the official documentation for nginx here:
* http://nginx.org/en/docs/

Please subscribe to nginx-announce mailing list to get
the most important news about nginx:
* http://nginx.org/en/support.html

Commercial subscriptions for nginx are available on:
* http://nginx.com/products/

----------------------------------------------------------------------
Unpacking nginx (1.14.2-1~bionic) ...
Selecting previously unselected package nginx-dbg.
Preparing to unpack nginx-dbg_1.14.2-1~bionic_amd64.deb ...
Unpacking nginx-dbg (1.14.2-1~bionic) ...
Setting up nginx (1.14.2-1~bionic) ...
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /lib/systemd/system/nginx.service.
Setting up nginx-dbg (1.14.2-1~bionic) ...
Processing triggers for ureadahead (0.100.0-20) ...
ureadahead will be reprofiled on next reboot
Processing triggers for systemd (237-3ubuntu10.3) ...
Processing triggers for man-db (2.8.3-2) ..
root@u1804atom:/usr/local/src#
```

### Change the "nginx.conf" file (attached):
```
Path: /etc/nginx/
```

### Download GeoIP files:
```
wget -O /usr/share/GeoIP/GeoIP.dat https://atlantistec.inf.br/GeoIP.dat
wget -O /usr/share/GeoIP/GeoLiteCity.dat https://atlantistec.inf.br/GeoLiteCity.dat
```

### Create dirs:
```
mkdir -p /var/ngx_pagespeed_cache
chown -R www-data:www-data /var/ngx_pagespeed_cache
mkdir /var/www
chown -R www-data:www-data /var/www/
mkdir /etc/nginx/sites-available
mkdir /etc/nginx/sites-enabled
```

### Create the "default" file (attached):
```
Path: /etc/nginx/sites-available
```

### Create symlink to "default" file:
```
cd /etc/nginx/sites-enabled/
ln -s ../sites-available/default .
```

### Restart nginx:
```
service nginx restart
```

# Install PHP 7.2-FPM
```
apt install php7.2-fpm php-apcu php7.2-gd php7.2-xml php7.2-mysql php7.2-opcache php7.2-mbstring php7.2-soap php7.2-xml php7.2-cli php7.2-intl php7.2-zip php7.2-curl
```

### Tuning FPM
```
pm.max_children = memory available / value of php process (ps -eo size,pid,user,command --sort -size | awk '{ hr=$1/1024 ; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' | grep php-fpm)

Example:
4Gb RAM
root@u1804hp:[~]: ps -eo size,pid,user,command --sort -size | awk '{ hr=$1/1024 ; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' | grep php-fpm
         6.76 Mb php-fpm: pool hp
         
4096 / 7 = 585

pm.start_servers - (cpu cores * 4)
pm.min_spare_servers - (cpu cores * 2)
pm.max_spare_servers - (cpu cores * 4)
pm.max_requests - max number of request by each php-fpm thread

### CONF FILE ###
[atom]
user = www-data
group = www-data

listen = 127.0.0.1:9000
listen.owner = www-data
listen.group = www-data
listen.mode = 0660

;Config Default

pm = dynamic
;pm = ondemand

php_value[session.save_handler] = files
php_value[session.save_path]    = /var/lib/php/sessions
php_value[soap.wsdl_cache_dir]  = /tmp

; Config High Traffic

;##1##
pm.max_children = 585
pm.start_servers = 32
pm.min_spare_servers = 16
pm.max_spare_servers = 32
pm.max_requests = 1000
;pm.process_idle_timeout = 15s
request_terminate_timeout = 6000s
;##1##


pm.status_path = /status
ping.path = /ping

env[HOSTNAME] = $HOSTNAME
env[PATH] = /usr/local/bin:/usr/bin:/bin
env[TMP] = /tmp
env[TMPDIR] = /tmp
env[TEMP] = /tmp
### CONF FILE ###
```

### Change the "php-fpm.conf" file:
```
Path: /etc/php/7.2/fpm
emergency_restart_threshold = 5
emergency_restart_interval = 1m
process_control_timeout = 10s
```

### Restart php-fpm:
```
service php7.2-fpm restart
```

### Check header:
```
root@u1804atom:~# curl -I localhost
HTTP/1.1 200 OK
Date: Thu, 14 Mar 2019 22:03:48 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 04 Dec 2018 14:44:49 GMT
Connection: keep-alive
Keep-Alive: timeout=10
Vary: Accept-Encoding
ETag: "5c0692e1-264"
Server: Microsoft IIS
X-Frame-Options: SAMEORIGIN
Accept-Ranges: bytes
```

### Check brotli:
```
root@u1804atom:~# curl -s -I -H 'Accept-Encoding: br' localhost
HTTP/1.1 200 OK
Date: Thu, 14 Mar 2019 22:11:59 GMT
Content-Type: text/html
Last-Modified: Tue, 04 Dec 2018 14:44:49 GMT
Connection: keep-alive
Keep-Alive: timeout=10
Vary: Accept-Encoding
ETag: W/"5c0692e1-264"
Server: Microsoft IIS
X-Frame-Options: SAMEORIGIN
Content-Encoding: br
```

### Check Pagespeed:
```
root@u1804atom:~# curl -Is http://192.168.15.253
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Keep-Alive: timeout=10
Vary: Accept-Encoding
Server: Microsoft IIS
X-Frame-Options: SAMEORIGIN
Date: Thu, 14 Mar 2019 22:09:40 GMT
X-Page-Speed: 1.13.35.2-0
Cache-Control: max-age=0, no-cache
```

### Check nginx modules:
`
root@u1804atom:~# nginx -V
nginx version: nginx/1.14.2
built by gcc 7.3.0 (Ubuntu 7.3.0-27ubuntu1~18.04)
built with OpenSSL 1.1.1b  26 Feb 2019
TLS SNI support enabled
configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module` **--add-module=/usr/local/src/nginx-http-flv-module --add-module=/usr/local/src/ngx_brotli --add-module=/usr/local/src/ngx_http_geoip2_module --add-module=/usr/local/src/headers-more-nginx-module --add-module=/usr/share/passenger/ngx_http_passenger_module --add-module=/usr/local/src/pagespeed-ngx-1.13.35.2-stable** `--with-http_geoip_module --with-cc-opt='-g -O2 -fdebug-prefix-map=/usr/local/src/nginx-1.14.2=. -specs=/usr/share/dpkg/no-pie-compile.specs -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-Wl,-Bsymbolic-functions -specs=/usr/share/dpkg/no-pie-link.specs -Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie'
`
###
Litecoin - LUs1p2BLcePR8pDQPXwekbye45EAsqwrWh
### :)
