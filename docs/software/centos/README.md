# Centos软件安装

> 这都是本人一步一步安装成功的，所以你可以按照下面的步骤进行安装，免得安装失败。

## 1、安装Redis

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

## 2、安装Rabbit消息中间件；
>centos 7.1 内核版本 --> 3.10.0-229.el7.x86_64  
>Erlang --> 19.0.4版本  
>RabbitMQ --> 3.6.14版本  

### 第一步、在线安装Erlang：
* 1)、执行下列命令，下载Erlang的rpm包：
```
cd usr/local/src/
wget http://www.rabbitmq.com/releases/erlang/erlang-19.0.4-1.el7.centos.x86_64.rpm
```

* 2)、执行下列命令，安装Erlang的rpm包：
```
rpm -ivh erlang-19.0.4-1.el7.centos.x86_64.rpm
yum -y install erlang
```

* 3)、执行下列命令，测试Erlang是否安装成功：
```
erl -version
结果显示：Erlang (SMP,ASYNC_THREADS,HIPE) (BEAM) emulator version 8.0.3
```

### 第二步、在线安装RabbitMQ：
* 1)、执行下列命令，下载rabbitMQ server的rpm包：
```
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/rabbitmq_v3_6_14/rabbitmq-server-3.6.14-1.el7.noarch.rpm
```

* 2)、执行下列命令，安装rabbitMQ server的rpm包：
```
yum install rabbitmq-server-3.6.14-1.el7.noarch.rpm
```

* 3)、执行下列命令，创建配置文件rabbitmq.config：
```
    cd /etc/rabbitmq/
    vim rabbitmq.config
    编辑内容如下：
    [{rabbit, [{loopback_users, []}]}].
    这里的意思是开放使用，rabbitmq默认创建的用户guest，密码也是guest，这个用户默认只能是本机访问，localhost或者127.0.0.1，从外部访问需要添加上面的配置。
    保存配置后重启服务：
    #service rabbitmq-server stop
    #service rabbitmq-server start
```

* 4)、安装插件
```
/sbin/rabbitmq-plugins enable rabbitmq_management 
```

* 5)、重启rabbitmq服务
```
service rabbitmq-server restart 
```
到此,就可以通过http://ip:15672 使用guest,guest 进行登陆web页面了
```
RabbitMQ的一些基本操作:
# 添加开机启动RabbitMQ服务
systemctl enable rabbitmq-server.service
# 查看服务状态
systemctl status  rabbitmq-server.service
# 启动服务
systemctl start rabbitmq-server.service
# 停止服务
systemctl stop rabbitmq-server.service
# 查看当前所有用户
rabbitmqctl list_users
# 查看默认guest用户的权限
rabbitmqctl list_user_permissions guest
# 由于RabbitMQ默认的账号用户名和密码都是guest。为了安全起见, 先删掉默认用户
rabbitmqctl delete_user guest
# 添加新用户
rabbitmqctl add_user username password
# 设置用户tag
rabbitmqctl set_user_tags username administrator
# 赋予用户默认vhost的全部操作权限
rabbitmqctl set_permissions -p / username ".*" ".*" ".*"
# 查看用户的权限
rabbitmqctl list_user_permissions username
更多关于rabbitmqctl的使用，可以参考帮助手册。
开启web管理接口
如果只从命令行操作RabbitMQ，多少有点不方便。幸好RabbitMQ自带了web管理界面，只需要启动插件便可以使用。
rabbitmq-plugins enable rabbitmq_management
访问:  http://localhost:15672
配置RabbitMQ
关于RabbitMQ的配置，可以下载RabbitMQ的配置文件模板到/etc/rabbitmq/rabbitmq.config, 然后按照需求更改即可。
关于每个配置项的具体作用，可以参考官方文档。
开启用户远程访问
默认情况下，RabbitMQ的默认的guest用户只允许本机访问， 如果想让guest用户能够远程访问的话，只需要将配置文件中的loopback_users列表置为空即可，如下：
{loopback_users, []}
另外关于新添加的用户，直接就可以从远程访问的，如果想让新添加的用户只能本地访问，可以将用户名添加到上面的列表, 如只允许admin用户本机访问。
{loopback_users, ["admin"]}
restart …
```