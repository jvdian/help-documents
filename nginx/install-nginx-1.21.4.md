# Install Nginx

**Soft version:** 1.21.4  
**OS version:** centos-release-7-8.2003.0.el7.centos.x86_64  
**Edit date:** 2021-11-24

-----

### 安装

安装关联程序和工具
```
yum install wget gcc-c++ make pcre-devel zlib-devel openssl-devel perl-Module-Install.noarch which -y
yum group install 'Development Tools' -y
```

创建目录
```
mkdir -p /opt/src/
mkdir -p /opt/data/nginx/run/
mkdir -p /opt/data/nginx/vhosts_conf/
```

下载关联程序源码 & 解压缩
```
cd /opt/src/
wget http://zlib.net/zlib-1.2.11.tar.gz
tar -zxf zlib-1.2.11.tar.gz
wget https://www.openssl.org/source/openssl-1.1.1d.tar.gz
tar -zxf openssl-1.1.1d.tar.gz
wget https://codeload.github.com/happyfish100/fastdfs-nginx-module/zip/refs/heads/master -O fastdfs-nginx-module-master.zip
unzip fastdfs-nginx-module-master.zip
```

切换目录
```
cd /opt/src/
```

下载安装包
```
wget http://nginx.org/download/nginx-1.21.4.tar.gz
```

解压缩
```
tar -zxf nginx-1.21.4.tar.gz
```

切换目录
```
cd nginx-1.21.4/
```

配置 & 编译安装
```
./configure \
--prefix=/opt/nginx \
--with-pcre \
--with-file-aio \
--with-http_realip_module \
--with-http_ssl_module \
--with-http_gzip_static_module \
--with-zlib=/opt/src/zlib-1.2.11 \
--with-openssl=/opt/src/openssl-1.1.1d \
--add-module=/opt/src/fastdfs-nginx-module-master/src
make && make install
```

复制文件
```
cp /opt/src/fastdfs-nginx-module-master/src/mod_fastdfs.conf /etc/fdfs/
```

编辑配置文件
```
vi /etc/fdfs/mod_fastdfs.conf
```
> 编辑如下项
> ```
> connect_timeout=10
> tracker_server=tracker服务器IP:22122
> storage_server_port=23000
> url_have_group_name = true
> store_path0=/data/fastdfs-storage
> ```

复制文件
```
cd /opt/src/fastdfs-master/conf/
cp http.conf mime.types /etc/fdfs/
```

编辑文件
```
vi /opt/nginx/conf/nginx.conf
```
> 编辑如下项
> ```
> pid /opt/data/nginx/run/nginx.pid;
> ```
> 找到`include mime.types;`添加如下项
> ```
> include /opt/data/nginx/vhosts_conf/*.conf;
> ```
> 在`server{...}`中添加如下项
> ```
> # fastdfs
> location ~/group([0-9])/M00 {
>     ngx_fastdfs_module;
> }
> ```

编辑文件
```
vi /etc/systemd/system/nginx.service
```
> 添加如下内容
> ```
> [Unit]
> Description=The NGINX HTTP and reverse proxy server
> After=syslog.target network.target remote-fs.target nss-lookup.target
> 
> [Service]
> Type=forking
> PIDFile=/opt/data/nginx/run/nginx.pid
> ExecStartPre=/opt/nginx/sbin/nginx -t
> ExecStart=/opt/nginx/sbin/nginx
> ExecReload=/bin/kill -s HUP $MAINPID
> ExecStop=/bin/kill -s QUIT $MAINPID
> PrivateTmp=true
> 
> [Install]
> WantedBy=multi-user.target
> ```

开机启动服务
```
systemctl daemon-reload
systemctl enable nginx
systemctl start nginx
```

允许端口通过防火墙
```
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```
