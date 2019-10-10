# Centos软件安装

> 这都是本人一步一步安装成功的，所以你可以按照下面的步骤进行安装，免得安装失败。

## 1.安装Redis

### 第一步：下载redis安装包
```
wget http://download.redis.io/releases/redis-4.0.9.tar.gz  
```
* 想下载哪个版本可以复制上面链接删除redis-*部分内容，可以查看到所有redis版本的安装包

### 第二步：解压安装包
```
tar -zxvf redis-4.0.9.tar.gz -C /usr/local/
```
* 将压缩包解压到指定目录,这里以**/usr/local/**为例。

### 第三步：安装gcc依赖
```
先通过gcc -v是否有安装gcc，
如果没有安装，执行命令sudo yum install -y gcc
```
### 第四步：cd到redis的解压目录下，并执行
```
cd /usr/local/redis-4.0.9/ 此处目录根据下载的redis版本及解压路径调整
```
### 第五步：编译安装
```
make MALLOC=libc 
```
* 将/usr/local/redis-4.0.9/src目录下的文件加到/usr/local/bin目录

```
cd src && make install
```
### 第六步：测试是否安装成功
```
cd /usr/local/redis-4.0.9/src/
./redis-server
```
* 如果显示蕾西以上部分信息，就已经安装成功了，ctrl+c关闭窗口

### 第七步：配置redis

* 以后台进程方式启动：
```
1.修改/usr/local/redis-4.0.9/redis.conf：    
    daemonize no   将值改为yes  --->保存退出
2.指定redis.conf文件启动：                           
    ./redis-server /usr/local/redis-4.0.6/redis.conf
```

* 设置redis远程连接：
```
a.因为redis默认设置允许本地连接，所以我们要将redis.conf中将
    bind 127.0.0.1 改为bind 0.0.0.0或者注释该行
b.另外，阿里云ECS有一个安全组，找到并添加规则允许6379端口访问
设置redis连接密码：
在redis.conf中搜索requirepass这一行，然后在合适的位置添加配置
requirepass 123456
设置完成后执行/usr/local/bin/redis-server /usr/local/redis-4.0.6/redis.conf 更新配置
```

### 第八步：设置开机自启动

* 由于上面我们执行了redis进程启动，通过ps -aux | grep redis查看redis进程，并用kill -9 进程id杀死

1、在/etc目录下新建redis目录
```
mkdir /etc/redis
```
2、将/usr/local/redis-4.0.9/redis.conf 文件复制一份到/etc/redis目录下，并命名为6379.conf
```
cp /usr/local/redis-4.0.9/redis.conf /etc/redis/6379.conf
```
3、将redis的启动脚本复制一份放到/etc/init.d目录下
```
cp /usr/local/redis-4.0.9/utils/redis_init_script /etc/init.d/redisd
```
4、设置redis开机自启动

* 先切换到/etc/init.d目录下,然后执行自启命令chkconfig redisd on,如果显示service redisd does not support chkconfig。解决方法：
使用vim编辑redisd文件，在第一行加入如下两行注释，保存退出
```
# chkconfig:   2345 90 10
# description:  Redis is a persistent key-value database
```

* 注释的意思是，redis服务必须在运行级2，3，4，5下被启动或关闭，启动的优先级是90，关闭的优先级是10。
再次执行开机自启命令chkconfig redisd on，这个时候应该就能成功了
现在可以直接已服务的形式启动和关闭redis了
```
启动：service redisd start
关闭：service redisd stop
```
