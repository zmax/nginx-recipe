Installation
============


```
sudo apt-get install nginx nginx-extra php5 php5-cli php5-fpm
```

修改 Nginx 設定檔:

`sudo vim /etc/nginx/nginx.conf`

```
user www-data;
worker_processes 4;
worker_cpu_affinity 0001 0010 0100 1000;
worker_rlimit_nofile 1024;
pid /run/nginx.pid;
 
events {
    use epoll;
    worker_connections 2048;
}
 
http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    server_names_hash_bucket_size 128;
 
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
 
    client_header_buffer_size 2k;
    large_client_header_buffers 4 4k;
    open_file_cache max=102400 inactive=20s;
  
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
 
    gzip on;
    gzip_disable "msie6";
 
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

- user：Nginx Server 啟動所使用的使用者 (ubuntu 預設用 www-data，CentOS 預設用 nginx)
- pid：ProcessID 存放位置 (CentOS 預設在 /var/run/nginx.pid)
- worker_processes：開啟的程序數量，請對應 CPU 核心數進行調整（多設無益）
- worker_cpu_affinity：每個程序所使用的邏輯核心，所以四核可以這樣設「0001 0010 0100 1000」，當然也可以一次設定兩個核心給同一個程序
- worker_rlimit_nofile：每個程序最高可以開啟的檔案數
- use epoll：啟動 epoll 會快很多，效果不錯
- worker_connections：每個程序最高可以開啟的連線數
- access_log, error_log：HTTP Log 存放的位置