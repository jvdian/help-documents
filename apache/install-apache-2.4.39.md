# Install Apache 

**Soft version:** 2.4.39  
**OS version:** centos-release-7-9.2009.1.el7.centos.x86_64  
**Edit date:** 2021-11-22  

-----

### 安装

安装关联程序和工具
```
yum install expat-devel keyutils-libs-devel krb5-devel zlib-devel openssl-devel -y
```

创建目录
```
mkdir -p /opt/src/
```

安装apr
```
cd /opt/src/
wget https://dlcdn.apache.org/apr/apr-1.7.0.tar.gz
tar -zxf apr-1.7.0.tar.gz
cd apr-1.7.0
./configure \
--prefix=/opt/apr
make && make install
```

安装apr-iconv
```
cd /opt/src/
wget https://dlcdn.apache.org/apr/apr-iconv-1.2.2.tar.gz
tar -zxf apr-iconv-1.2.2.tar.gz
cd apr-iconv-1.2.2
./configure \
--prefix=/opt/apr-iconv \
--with-apr=/opt/apr
make && make install
```

安装apr-util
```
cd /opt/src/
wget https://dlcdn.apache.org/apr/apr-util-1.6.1.tar.gz
tar -zxf apr-util-1.6.1.tar.gz
cd apr-util-1.6.1
./configure \
--prefix=/opt/apr-util \
--with-apr=/opt/apr \
--with-apr-iconv=/opt/apr-iconv/bin/apriconv
make && make install
```

安装pcre
```
cd /opt/src/
wget https://nchc.dl.sourceforge.net/project/pcre/pcre/8.45/pcre-8.45.tar.gz
tar -zxf pcre-8.45.tar.gz
cd pcre-8.45
./configure \
--prefix=/opt/pcre
make && make install
```

切换目录
```
cd /opt/src/
```

下载安装包
```
wget https://dlcdn.apache.org/httpd/httpd-2.4.39.tar.gz
```

解压缩
```
tar -zxf httpd-2.4.39.tar.gz
```

切换目录
```
cd /opt/src/httpd-2.4.39/
```

编辑文件（用于应对大访问量）
```
vi /opt/src/httpd-2.4.39/server/mpm/prefork/prefork.c
```
> 编辑如下项
> ```
> define DEFAULT_SERVER_LIMIT 5000
> define MAX_SERVER_LIMIT 200000
> ```

移动文件夹
```
mv /opt/src/apr-1.7.0 /opt/src/httpd-2.4.39/srclib/apr
mv /opt/src/apr-util-1.6.1 /opt/src/httpd-2.4.39/srclib/apr-util
```

配置 & 编译安装
```
./configure \
--prefix=/opt/apache \
--with-pcre=/opt/pcre \
--with-apr=/opt/apr \
--with-apr-util=/opt/apr-util \
--with-mysql=shared,mysqlnd \
--enable-so \
--enable-ssl \
--enable-deflate \
--enable-rewrite \
--enable-headers \
--enable-expires \
--disable-cgid
make && make install
```

编辑文件
```
vi /opt/apache/conf/httpd.conf 
```
> 文件末尾添加如下项
> ```
> # 禁止显示或发送服务器信息
> TraceEnable off
> ServerTokens Prod
> ServerSignature off
> ```
> 取消以下语句注释符
> ```
> Include conf/extra/httpd-mpm.conf
> Include conf/extra/httpd-vhosts.conf
> ```
> 找到`<Directory />`编辑如下项
> ```
> <Directory />
>     Order Deny,Allow
>     Allow from All
>     Options FollowSymLinks
>     AllowOverride All
> </Directory>
> ```

编辑文件（用于应对大访问量）
```
vi /opt/apache/conf/extra/httpd-mpm.conf
```
> 编辑如下项
> ```
> <IfModule mpm_prefork_module>
>     StartServers       15
>     MinSpareServers    100
>     MaxSpareServers    300
>     MaxRequestWorkers  5000
> ```

复制文件 & 创建链接
```
cp /opt/apache/bin/apachectl /etc/rc.d/init.d/httpd
ln -s /etc/rc.d/init.d/httpd /etc/rc.d/rc3.d/S61httpd
```

编辑文件
```
vi /etc/rc.d/init.d/httpd
```
> 在`#!/bin/sh`下添加如下项
> ```
> #chkconfig:35 61 61
> #description:Apache
> ```

开机启动服务
```
chkconfig --add httpd
chkconfig httpd on
```

-----

### **添加PHP-FPM支持**

编辑文件
```
vi /opt/apache/conf/httpd.conf
```
> 取消以下语句注释符
> ```
> LoadModule proxy_module modules/mod_proxy.so
> LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so
> ```
> 文件末尾添加如下项
> ```
> <FilesMatch \.php$>
> SetHandler "proxy:fcgi://127.0.0.1:9000"
> </FilesMatch>
> ```
