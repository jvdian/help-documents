# Install MySQL

**Soft version:** 5.7.24  
**OS version:** CentOS Linux release 7.5.1804 (Core)  
**Edit date:** 2018-11-22  

-----

**安装前确认内存至少有2G**  
**安装参考：https://dev.mysql.com/doc/refman/5.7/en/source-installation.html**

-----

添加用户组
```
groupadd mysql
```

添加禁止登录的用户
```
useradd mysql -g mysql -s /bin/false
```

安装关联程序和工具
```
yum install gcc gcc-c++ make cmake ncurses-devel bison perl-Module-Install.noarch -y
```

创建目录
```
mkdir -p /opt/src/
mkdir -p /data/mysql/data
mkdir -p /data/mysql/conf
```

修改目录权限
```
chown -R mysql:mysql /data/mysql/
```

安装Boost
```
cd /opt/src/
wget http://nchc.dl.sourceforge.net/project/boost/boost/1.59.0/boost_1_59_0.tar.gz
tar -zxf boost_1_59_0.tar.gz
cd boost_1_59_0/
./bootstrap.sh
./b2 stage threading=multi link=shared
./b2 install threading=multi link=shared
```

切换目录
```
cd /opt/src/
```

下载安装包
```
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.24.tar.gz
```

解压缩
```
tar -zxf mysql-5.7.24.tar.gz
```

切换目录
```
cd mysql-5.7.24/
```

配置 & 编译安装
```
cmake . \
-DCMAKE_INSTALL_PREFIX=/opt/mysql \
-DMYSQL_DATADIR=/data/mysql/data \
-DSYSCONFDIR=/data/mysql/conf \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DMYSQL_TCP_PORT=3306 \
-DENABLED_LOCAL_INFILE=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1
make && make install
```

移除系统默认的配置文件（如果存在）
```
mv /etc/my.cnf /etc/my.cnf.bak
```

初始化数据库
```
/opt/mysql/bin/mysqld --initialize-insecure --user=mysql --basedir=/opt/mysql --datadir=/data/mysql/data
```

编辑文件
```
vi /opt/mysql/my.cnf
```
> 添加如下内容
```
[mysqld]
basedir = /opt/mysql
datadir = /data/mysql/data
port = 3306
user = mysql
```

编辑文件
```
cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld
vi /etc/rc.d/init.d/mysqld
```
> 编辑如下项
```
basedir=/opt/mysql
datadir=/data/mysql/data
```

开机启动服务
```
chmod +x /etc/init.d/mysqld
chkconfig mysqld on
service mysqld start
```

把MySQL的库文件链接到系统默认的位置
> 在编译类似PHP等软件时可以不用指定MySQL的库文件地址
```
ln -s /opt/mysql/lib /usr/lib/mysql
ln -s /opt/mysql/include/mysql /usr/include/mysql
```

添加环境变量
```
vi /etc/profile
```
> 添加如下项在文件末尾
```
export PATH=$PATH:/opt/mysql/bin
```

重载环境变量
```
source /etc/profile
```

安全设置
```
mysql_secure_installation
```
