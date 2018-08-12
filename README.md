# test
docker

首先安装docker yum -y install docer
启动 service docker start
拉取docker centos 镜像 docker pull centos
查看镜像 docker images

创建容器并进入 docker run -it  centos /bin/bash

尝试从官网更新EPEL rpm -Uvh  https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
更新源  rpm -Uvh https://centos7.iuscommunity.org/ius-release.rpm
安装 nginx yum -y install nginx
修改 nginx配置
 user  nginx;
worker_processes  4;
worker_cpu_affinity 00000001 00000010 00000100 00001000;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

worker_rlimit_nofile 65535;


events {
    use epoll;
    worker_connections  1024;
}


http {
    server_tokens off;
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '"$remote_addr"  "$remote_user" "[$time_local]" "$request" '
                      '"$status $body_bytes_sent" "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    charset	utf-8;
    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 64k;
    client_max_body_size 8m;
    sendfile on;
    tcp_nopush     on;
    tcp_nodelay on;
    keepalive_timeout 60;
    fastcgi_intercept_errors on;
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;
    
    open_file_cache max=65535 inactive=10s;
    open_file_cache_valid 30s;
    open_file_cache_min_uses 1;
    
    gzip on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 2;
    gzip_types       text/plain application/javascript text/css application/xml  text/xml image/jpeg ;
    gzip_vary on;
    

    include /etc/nginx/conf.d/*.conf;
}


安装php7.2 yum -y install php72u php72u-cli  php72u-fpm  php72u-mysqli  php72u-json  php72u-xml
修改 php-fpm配置
user = nginx
group = nginx


启动 nginx -t php
安装supervisorctl yum -y install supervisorctl
查看 supervisorctl 启动项 supervisorctl status
查看目录 whereis nginx whereis php
配置 启动项目 
vi /etc/supervisord.d/php.ini
[program:nginx]
command=/usr/sbin/nginx    -g "daemon off;"
process_name=%(program_name)s
numprocs=1
umask=022
priority=999
autostart=true
autorestart=true
startsecs=10
startretries=3
exitcodes=0,2
stopsignal=QUIT
stopwaitsecs=10
user=root
redirect_stderr=true
stdout_logfile=/var/log/nginx/nginx.log
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stdout_events_enabled=false
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB
stderr_events_enabled=false

[program:php]
command=/usr/sbin/php-fpm -F
process_name=%(program_name)s
numprocs=1
umask=022
priority=999
autostart=true
autorestart=true
startsecs=10
startretries=3
exitcodes=0,2
stopsignal=QUIT
stopwaitsecs=10
user=root
redirect_stderr=true
stdout_logfile=/var/log/nginx/php.log
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stdout_events_enabled=false
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB
stderr_events_enabled=false

退出docker

查看容器 docker ps -a
提交容器修改  docker commit -a"doozz" -m"v3" 9f2e51aaa80d  centos:v1

docker images

创建 
启动新容器
docker run -p 80:80 --name nginx -v /root/www:/usr/share/nginx/html -v /root/nginx/conf/wp.conf:/etc/nginx/conf.d/wp.conf  -d centos:v2 supervisord -n

查看新容器
docker ps

docker 拉取mysql docker pull mysql
运行容器
docker run -p 3306:3306 --name mysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
进入容器
docker exex -it mysql /bin/bash
连接mysql mysql -h127.0.0.1 -p3306 -uroot -p123456 这里的ip是服务器ip而非容器ip
