# Install Fastdfs

**Soft version:** FastDFS V6.07  
**OS version:** centos-release-7-9.2009.1.el7.centos.x86_64  
**Edit date:** 2021-11-23

-----

### 安装

安装关联程序和工具
```
yum install gcc gcc-c++ libevent unzip perl -y
```

创建目录
```
mkdir -p /opt/src/
```

安装libfastcommon
```
cd /opt/src/
wget https://github.com/happyfish100/libfastcommon/archive/refs/heads/master.zip -O libfastcommon-master.zip
unzip libfastcommon-master.zip
cd libfastcommon-master/
./make.sh && ./make.sh install
ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so 
```

安装FastDFS
```
cd /opt/src/
wget https://codeload.github.com/happyfish100/fastdfs/zip/refs/heads/master -O fastdfs-master.zip
unzip fastdfs-master.zip
cd fastdfs-master/
./make.sh && ./make.sh install
```

-----

### 安装Client

创建目录
```
mkdir -p /data/fastdfs-client
```

切换目录
```
cd /opt/src/fastdfs-master/php_client
```

编译PHP扩展
```
/opt/php5/bin/phpize 
./configure --with-php-config=/opt/php5/bin/php-config
make && make install
```

将`/opt/src/fastdfs-master/php_client/fastdfs_client.ini`中配置项添加到`/opt/php5/etc/php.ini`

编辑文件
```
vi /etc/fdfs/client.conf
```
> 编辑如下项
> ```
> connect_timeout = 10
> base_path = /data/fastdfs-client
> tracker_server = Tracker服务器的IP:22122
> http.tracker_server_port = 8080
> ```

-----

### 安装Tracker

创建目录
```
mkdir -p /data/fastdfs-tracker
```

编辑文件
```
vi /etc/fdfs/tracker.conf
```
> 编辑如下项 
> ```
> base_path=/data/fastdfs-tracker
> http.server_port = 8080
> ```

创建链接
```
ln -s /usr/bin/fdfs_trackerd /usr/local/bin
ln -s /usr/bin/fdfs_storaged /usr/local/bin
```

编辑文件
```
vi /etc/rc.d/rc.local
```
> 文件末尾添加如下项
> ```
> fdfs_trackerd /etc/fdfs/tracker.conf start
> ```

开机启动服务
```
chmod +x /etc/rc.d/rc.local
fdfs_trackerd /etc/fdfs/tracker.conf start
```

允许端口通过防火墙
```
firewall-cmd --permanent --add-port=22122/tcp
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --reload
```

-----

### 安装Storage

*完成安装Storage后需要安装Nginx。FastDFS通过Tracker服务器，将文件放在Storage服务器存储，但是同组Storage服务器之间文件复制会有同步延迟的问题。当文件通过Client上传到StorageA，同组的StorageB将要进行同步。如果同步没有完成。这时候访问StorageB的这个文件，会返回错误。使用fastdfs-nginx-module当StorageB上没有找到该文件时会查找StorageA上的文件。*

创建目录
```
mkdir -p /data/fastdfs-storage
```

编辑文件
```
vi /etc/fdfs/storage.conf
```
> 编辑如下项
> ```
> base_path = /data/fastdfs-storage
> store_path0 = /data/fastdfs-storage
> tracker_server = Tracker服务器的IP:22122
> http.server_port = 8080
> ```

创建链接
```
ln -s /usr/bin/fdfs_trackerd /usr/local/bin
ln -s /usr/bin/fdfs_storaged /usr/local/bin
```

编辑文件
```
vi /etc/rc.d/rc.local
```
> 文件末尾添加如下项
> ```
> fdfs_storaged /etc/fdfs/storage.conf start
> ```

开机启动服务
```
chmod +x /etc/rc.d/rc.local
fdfs_storaged /etc/fdfs/storage.conf start
```

允许端口通过防火墙
```
firewall-cmd --permanent --add-port=23000/tcp
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --reload
```

-----

### 测试

命令行
```
fdfs_upload_file /etc/fdfs/client.conf 文件路径
```

PHP代码
```
<?php
$fdfs = new FastDFS();
$fdfs->tracker_get_connection();
echo $fdfs->storage_upload_by_filebuff1("test-" . time(), "txt");
$fdfs->tracker_close_all_connections();
```
