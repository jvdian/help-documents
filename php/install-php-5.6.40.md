# Install PHP

**Soft version:** 5.6.40  
**OS version:** centos-release-7-9.2009.1.el7.centos.x86_64  
**Edit date:** 2021-11-23

-----

### 安装

添加用户组
```
groupadd serverwork
```

添加禁止登录的用户
```
useradd sw_lnmp -g serverwork -s /bin/false
```

安装关联程序和工具
```
yum install gcc gcc-c++ make cmake epel-release -y  
yum install curl-devel freetype-devel libgif-devel libjpeg-devel libtidy-devel -y 
yum install libttf-devel libpng-devel gd-devel gd2-devel t1lib-devel -y  
yum install libxml2-devel openssl-devel pcre-devel zlib-devel libtidy -y  
yum install autoconf m4 openldap openldap-devel libicu-devel -y  
```

没装mysql需额外执行
```
yum install mysql-devel -y
```

复制文件
```
cp -frp /usr/lib64/libldap* /usr/lib/
```

创建目录
```
mkdir -p /opt/src/
```

切换目录
```
cd /opt/src/
```

下载安装包
```
wget http://cn2.php.net/distributions/php-5.6.40.tar.gz
```

解压缩
```
tar -zxf php-5.6.40.tar.gz
```

切换目录
```
cd /opt/src/php-5.6.40/
```

配置 & 编译安装
```
./configure \
--prefix=/opt/php5 \
--enable-fpm \
--with-fpm-user=sw_lnmp \
--with-fpm-group=serverwork \
--with-config-file-path=/opt/php5/etc \
--with-regex \
--with-openssl \
--with-zlib \
--with-zlib-dir \
--enable-bcmath \
--with-curl \
--enable-exif \
--with-pcre-dir \
--enable-ftp \
--with-gd \
--with-jpeg-dir \
--with-png-dir \
--with-xpm-dir \
--with-freetype-dir \
--with-t1lib \
--enable-gd-native-ttf \
--with-gettext \
--enable-mbstring \
--with-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--enable-opcache \
--enable-pcntl \
--with-pdo-mysql=mysqlnd \
--enable-soap \
--enable-sockets \
--with-tidy \
--with-xmlrpc \
--with-iconv-dir \
--enable-zip \
--enable-mysqlnd \
--with-apxs2=/opt/apache/bin/apxs \
--enable-mysqlnd-compression-support \
--with-libxml-dir \
--enable-xml \
--disable-rpath \
--enable-shmop \
--enable-sysvsem \
--enable-inline-optimization \
--enable-mbregex \
--enable-intl \
--with-libmbfl \
--with-mhash \
--disable-fileinfo \
--with-pear \
--enable-maintainer-zts \
--with-ldap=shared \
--without-gdbm
make && make install
```

复制文件
```
cp php.ini-development /opt/php5/etc/php.ini
```
编辑文件
```
vi /opt/php5/etc/php.ini
```
> 编辑如下项
> ```
> # 列出PHP可以禁用的函数，如果某些程序需要用到这个函数，可以删除，取消禁用。
> disable_functions = passthru,exec,system,chroot,scandir,chgrp,chown,shell_exec,proc_open,proc_get_status,ini_alter,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,escapeshellcmd,dll,popen,disk_free_space,checkdnsrr,checkdnsrr,getservbyname,getservbyport,disk_total_space,posix_ctermid,posix_get_last_error,posix_getcwd,posix_getegid,posix_geteuid,posix_getgid,posix_getgrgid,posix_getgrnam,posix_getgroups,posix_getlogin,posix_getpgid,posix_getpgrp,posix_getpid,posix_getppid,posix_getpwnam,posix_getpwuid,posix_getrlimit,posix_getsid,posix_getuid,posix_isatty,posix_kill,posix_mkfifo,posix_setegid,posix_seteuid,posix_setgid,posix_setpgid,posix_setsid,posix_setuid,posix_strerror,posix_times,posix_ttyname,posix_uname
> # php支持opcode缓存
> opcache.enable=1
> # 去掉前面的分号
> opcache.enable_cli=0
> ```
> 文件末尾添加如下项
> ```
> # 开启opcode缓存功能
> zend_extension=opcache.so
> ```

复制文件
```
cp /opt/php5/etc/php-fpm.conf.default /opt/php5/etc/php-fpm.conf
```

编辑文件
```
vi /opt/php5/etc/php-fpm.conf
```
> 编辑如下项
> ```
> pid=run/php-fpm.pid
> ```

开机启动服务
```
cp /opt/src/php-5.6.38/sapi/fpm/init.d.php-fpm /etc/rc.d/init.d/php-fpm
chmod +x /etc/rc.d/init.d/php-fpm
chkconfig php-fpm on
```

添加环境变量
```
vi /etc/profile
```
> 文件末尾添加如下项
> ```
> PATH=$PATH:/opt/php5/bin
> PATH=$PATH:/opt/php5/sbin
> ```

重载环境变量
```
source /etc/profile
```
