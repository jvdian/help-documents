# PHP Command

**Soft version:** 5.6.38  
**OS version:** CentOS Linux release 7.5.1804 (Core)  
**Edit date:** 2018-11-23  

-----

查看php扩展
```
php-m
```

-----

#### 安装memcache扩展

安装Memcached
```
yum install memcached -y
```

开机启动服务
```
systemctl enable memcached.service
```

切换目录
```
cd /opt/src/
```

下载安装包
```
wget http://pecl.php.net/get/memcache-3.0.8.tgz
```

解压缩
```
tar -zxf memcache-3.0.8.tgz
```

切换目录
```
cd memcache-3.0.8/
```

安装PHP扩展模块
```
phpize
./configure \
--with-php-config=/opt/php5/bin/php-config \
--enable-memcache \
--with-zlib-dir
make && make install
```

编辑文件
```
vi /opt/php5/etc/php.ini
```
> 编辑如下项
```
session.save_handler = memcache
session.save_path = "127.0.0.100:11211"
```
> 添加如下项在文件末尾
```
extension=memcache.so
```

重载php-fpm
```
service php-fpm restart
```

-----

#### 安装Sphinx扩展

切换目录
```
cd /opt/src/
```

下载安装包
```
wget http://pecl.php.net/get/sphinx-1.3.3.tgz
```

解压缩
```
tar -zxf sphinx-1.3.3.tgz
```

切换目录
```
cd sphinx-1.3.3/
```

安装PHP扩展模块
```
phpize
```

安装libsphinxclient
```
cd /opt/src/coreseek-4.1-beta/testpack/api/libsphinxclient/
./configure
make && make install
```

切换目录
```
cd /opt/src/sphinx-1.3.3/
```

配置 & 编译安装
```
./configure --with-php-config=/opt/php5/bin/php-config --with-sphinx
make && make install
```

编辑文件
```
vi /opt/php5/etc/php.ini
```
> 添加如下项在文件末尾
```
extension=sphinx.so
```

-----

#### 安装swoole扩展

切换目录
```
cd /opt/src/
```

下载安装包
```
wget https://github.com/swoole/swoole-src/archive/v1.10.6.tar.gz
```

解压缩
```
tar -zxf swoole-src-1.10.6.tar.gz
```

切换目录
```
cd swoole-src-1.10.6/
```

安装PHP扩展模块
```
phpize
./configure --enable-sockets --enable-openssl --enable-swoole
make && make install
```

编辑文件
```
vi /opt/php5/etc/php.ini
```
> 添加如下项在文件末尾
```
extension=swoole.so
```

-----

#### 安装ImageMagick扩展

安装ImageMagick
> 这里下载的是当前最新版安装源码包，解压出的目录名会根据版本号变化
```
cd /opt/src/
wget http://www.imagemagick.org/download/ImageMagick.tar.gz
tar -zxf ImageMagick.tar.gz
cd ImageMagick-7.0.8-14/
./configure --prefix=/opt/imagemagick
make && make install
```

切换目录
```
cd /opt/src/
```

下载安装包
```
wget http://pecl.php.net/get/imagick-3.4.3.tgz
```

解压缩
```
tar -zxf imagick-3.4.3.tgz
```

切换目录
```
cd imagick-3.4.3/
```

安装PHP扩展模块
```
phpize
./configure --with-php-config=/opt/php5/bin/php-config --with-imagick=/opt/imagemagick
make && make install
```

编辑文件
```
vi /opt/php5/etc/php.ini
```
> 添加如下项在文件末尾
```
extension=imagick.so
```

-----

#### 安装Redis扩展

切换目录
```
cd /opt/src/
```

下载安装包
```
wget http://pecl.php.net/get/redis-4.3.0.tgz
```

解压缩
```
tar -zxf redis-4.3.0.tgz
```

切换目录
```
cd redis-4.3.0
```

安装PHP扩展模块
```
phpize
./configure
make && make install
```

编辑文件
```
vi /opt/php5/etc/php.ini
```
> 添加如下项在文件末尾
```
extension=redis.so
```

-----

#### 安装GMP扩展

安装关联程序和工具
```
yum install gmp gmp-devel -y
```

进入源码包目录
```
cd /opt/src/php-5.6.40/ext/gmp/
```

配置 & 编译安装
```
phpize
./configure --with-php-config=/opt/php5/bin/php-config --with-gmp
make && make install
```

编辑文件
```
vi /opt/php5/etc/php.ini 
```
> 文件末尾添加如下项
> ```
> extension=gmp.so
> ```

重启服务
```
systemctl restart php-fpm
```
