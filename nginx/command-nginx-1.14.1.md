# Nginx Command

**Soft version:** 1.14.1  
**OS version:** CentOS Linux release 7.5.1804 (Core)  
**Edit date:** 2018-11-22  

-----

查看Nginx安装模块
```
/opt/nginx/sbin/nginx -V
```

-----

#### Nginx HTTPS代理配置格式

```
server {
    listen 443;
    server_name 域名;

    ssl on;
    ssl_certificate 证书绝对路径.crt;
    ssl_certificate_key 证书绝对路径.key;

    #charset koi8-r;

    #access_log logs/host.access.log main;

    client_max_body_size 10M;
    fastcgi_connect_timeout 150s;
    fastcgi_send_timeout 150s;
    fastcgi_read_timeout 150s;

    location / {
        proxy_pass_header Server;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Scheme $scheme;
        proxy_pass http://127.0.0.1:8080;
    }

    # 静态资源直接用nginx
    # location /static {
    #    root        绝对路径;
    #    expires     1d;
    #    add_header  Cache-Control public;
    #    access_log  off;
    # }

    # error_page 404 /404.html;
    # redirect server error pages to the static page /50x.html
    #
    # error_page 500 502 503 504 /50x.html;
    # location = /50x.html {
    #       root /data/nginx/sites/cloud2_auth.gracepsy.new/public;
    # }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    location ~ /\.ht {
            deny all;
    }
}
```


-----

#### Nginx PHP代理配置格式

```
server {
    server_name #####your_domain#####;
    listen 80;
    # listen 443;
    # ssl on;
    # ssl_certificate crt文件路径;
    # ssl_certificate_key key文件路径;

    # charset koi8-r;

    # access_log logs/host.access.log main;
    # access_log 日志存放路径 main;
    # 利用cron+shell脚本可以实现按年月日分割access_log为多个文件

    location / {
        root #####your_path#####;
        index index.html index.htm index.php;
        # url rewrite
        # rewrite ^/(\w+)\.html$ /$1.php;
        # rewrite ^/(\w+)/(\w+)$ /$1/$2.php;
    }

    # error_page 404 /404.html;
    # redirect server error pages to the static page /50x.html
    #
    # error_page 500 502 503 504 /50x.html;
    # location = /50x.html {
    #   root #####your_path#####;
    # }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    # location ~ \.php$ {
    #    proxy_pass http://127.0.0.1;
    # }

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root #####your_path#####;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    location ~ /\.ht {
        deny all;
    }
}
```

-----
