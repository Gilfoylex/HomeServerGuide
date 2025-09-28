# 安装nginx
tdnf install nginx


 tdnf install tar

vi account.conf  添加一行 cloudflare的token
export CF_Token='your token'

# 改回使用letsencrypt
~/.acme.sh/acme.sh --set-default-ca --server letsencrypt

# 注册邮箱
 ~/.acme.sh/acme.sh --register-account -m ssl@test.com

 # 签发证书
 ~/.acme.sh/acme.sh --issue --dns dns_cf -d example.com -d '*.example.com'


# 安装证书
```
mkdir -p /etc/ssl/example

~/.acme.sh/acme.sh --install-cert -d example.com \
  --key-file       /etc/ssl/example/example.key  \
  --fullchain-file /etc/ssl/example/example.crt  \
  --reloadcmd     "systemctl reload nginx"
```


# 查看端口占用

ss -tuln | grep 23341

# 查看防火墙规则
## ipv4
iptables -L -n -v
## ipv6
iptables -L -n -v

# 防火墙放行
iptables -I INPUT -p tcp --dport 80 -j ACCEPT

# 删除防火墙放行
iptables -D INPUT -p tcp --dport 80 -j ACCEPT


# 添加防火墙的脚本(nginx-fw.sh)
```
#!/bin/bash

sleep 5

IMMICH_PORT=23341
# 检查是否已添加，避免重复
iptables -C INPUT -p tcp --dport $IMMICH_PORT -j ACCEPT 2>/dev/null || \
    iptables -I INPUT -p tcp --dport $IMMICH_PORT -j ACCEPT

ip6tables -C INPUT -p tcp --dport $IMMICH_PORT -j ACCEPT 2>/dev/null || \
    ip6tables -I INPUT -p tcp --dport $IMMICH_PORT -j ACCEPT

```

# 使用定时任务增加开机增加防火墙规则
```
crontab -e
@reboot /root/nginx-fw.sh
```


# 简单的nginx.conf

```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    # server {
    #     listen       80;
    #     server_name  localhost;

    #     #charset koi8-r;

    #     #access_log  logs/host.access.log  main;

    #     location / {
    #         root   html;
    #         index  index.html index.htm;
    #     }

    #     #error_page  404              /404.html;

    #     # redirect server error pages to the static page /50x.html
    #     #
    #     error_page   500 502 503 504  /50x.html;
    #     location = /50x.html {
    #         root   html;
    #     }

    #     # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #     #
    #     #location ~ \.php$ {
    #     #    proxy_pass   http://127.0.0.1;
    #     #}

    #     # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #     #
    #     #location ~ \.php$ {
    #     #    root           html;
    #     #    fastcgi_pass   127.0.0.1:9000;
    #     #    fastcgi_index  index.php;
    #     #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #     #    include        fastcgi_params;
    #     #}

    #     # deny access to .htaccess files, if Apache's document root
    #     # concurs with nginx's one
    #     #
    #     #location ~ /\.ht {
    #     #    deny  all;
    #     #}
    # }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    
    server {
       listen 23341 ssl;          # IPv4
       listen [::]:23341 ssl;     # IPv6
       server_name  _;

       ssl_certificate      /etc/ssl/aa/aa.crt;
       ssl_certificate_key  /etc/ssl/aa/aa.key;

       ssl_session_cache    shared:SSL:1m;
       ssl_session_timeout  5m;

       ssl_ciphers  HIGH:!aNULL:!MD5;
       ssl_prefer_server_ciphers  on;

       location / {
           root   html;
           index  index.html index.htm;
       }
    }

}

```




