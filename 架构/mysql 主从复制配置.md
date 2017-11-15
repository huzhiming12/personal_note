---
title: mysql主从复制配置
categories: 架构
date: 2017-07-30 16:34:14
---

#### 数据库安装

```shell
sudo apt-get update //更新系统
sudo apt-get install mysql-server //安装MySQL，中间会让输入密码
```

安装完成之后，设置数据库的远程访问权限：

```shell
GRANT ALL PRIVILEGES ON *.* TO'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
```

其中root是用户名，123456是密码，‘%’表示所有IP都能访问。

最后如果远程连接出现：MySQL远程连接ERROR 2003 (HY000):Can't connect to MySQL server on'XXXXX'(111)  之类的问题。

解决方法：修改云主机上的/etc/mysql/my.cnf 文件，注释掉 bind_address=127.0.0.1。这句ok。

*注：如果mysql.cnf 中没有bind_address,则在etc/mysql/mysql.conf.d/mysqld.cnf中*

#### 修改主库的配置文件

```
sudo vim /etc/mysql/my.cnf
```

添加如下内容：

```shell
[mysqld]
##[必须]启用二进制日志
log-bin=mysql-bin   
###[必须]服务器唯一ID，默认是1，一般取IP最后一段
server-id=222      
#指定同步的数据库，如果不指定则同步全部数据库
binlog-do-db=seckill
```

#### 修改从库的配置文件

```shell
[mysqld]
log-bin=mysql-bin   //[不是必须]启用二进制日志
server-id=226      //[必须]服务器唯一ID，默认是1，一般取IP最后一段
```

#### 重启两台MySQL

```shell
service mysql restart
```

#### 在master服务器上创建账户并授权

```mysql
GRANT REPLICATION SLAVE ON *.* to 'user'@'%' identified by '123456'; //一般不用root帐号，'%'表示所有客户端都可能连，只要帐号，密码正确，此处可用具体客户端IP代替，如192.168.145.226，加强安全。
```

#### 在主服务器中查询master状态

```mysql
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000002 |     1372 | secondkill   |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

```

这里需要记住**| mysql-bin.000002 |     1372 |** 这两个参数

#### 配置从服务器

```mysql
mysql>change master to master_host='192.168.145.222',master_user='mysync',master_password='q123456',
         master_log_file='mysql-bin.000002',master_log_pos=1372;   //注意不要断开，308数字前后无单引号。
mysql>start slave;    //启动从服务器复制功能
```

#### 查看从服务器状态

```mysql
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 120.77.87.222
                  Master_User: slave1
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000002
          Read_Master_Log_Pos: 1372
               Relay_Log_File: VM-58-249-ubuntu-relay-bin.000004
                Relay_Log_Pos: 597
        Relay_Master_Log_File: mysql-bin.000002
             Slave_IO_Running: Yes  #这两个状态必须是Yes
            Slave_SQL_Running: Yes
            ……………………
```

Slave_IO及Slave_SQL进程必须正常运行，即YES状态，否则都是错误的状态(如：其中一个NO均属错误)。