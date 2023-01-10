# Install Redis

**Soft version:** 6.2.6
**OS version:** centos-release-7-6.1810.2.el7.centos.x86_64
**Edit date:** 2022-03-25

-----

安装关联程序和工具
```
yum install wget gcc-c++ make -y
```

创建目录
```
mkdir -p /opt/src/
mkdir -p /data/redis/
mkdir -p /data/redis/run/
mkdir -p /opt/redis/
```

修改目录权限
```
chown nobody:nobody /data/redis
```

切换目录
```
cd /opt/src/
```

下载安装包
```
wget https://download.redis.io/releases/redis-6.2.6.tar.gz
```

解压缩
```
tar -zxf redis-6.2.6.tar.gz
```

切换目录
```
cd redis-6.2.6/
```

编译安装
```
make && make install
```

编辑文件
```
cp /opt/src/redis-6.2.6/redis.conf /opt/redis/redis.conf
vi /opt/redis/redis.conf
```
> 编辑如下项
```
port 端口号
# bind 127.0.0.1 允许外网访问
pidfile /data/redis/run/redis.pid
logfile /data/redis/redis.log
dir /data/redis/
appendonly yes
appendfsync always / everysec / no

```
>> appendfsync根据实际应用环境选择其一  
1. always 每条命令写入一次。最稳定，性能最不好。  
2. everysec 每秒写入一次。折中，推荐方式。  
3. no 依赖OS写入。性能最好，最不稳定。

编辑文件
```
vi /etc/systemd/system/redis.service
```
> 添加如下内容
```
> [Unit]
> Description=The redis server
> After=network.target
> 
> [Service]
> Type=simple
> PIDFile=/data/redis/run/redis.pid
> User=nobody
> ExecStart=/usr/local/bin/redis-server /opt/redis/redis.conf
> ExecStop=/bin/kill -s SIGTERM $MAINPID
> PrivateTmp=true
> 
> [Install]
> WantedBy=multi-user.target
```

编辑文件
```
vi /opt/redis/redis.conf
```
> 编辑如下项
>> 允许外部直接访问
```
# bind
protected-mode no
```
>> 允许外部使用密码访问
```
# bind
requirepass 密码
```

开机启动服务
```
systemctl daemon-reload
systemctl enable redis
systemctl start redis
```

允许端口通过防火墙
```
firewall-cmd --permanent --add-port=6379/tcp
firewall-cmd --reload
```
