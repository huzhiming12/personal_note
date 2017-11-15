---
title: ubuntu Nginx+Tomcat负载均衡配置
date: 2017-07-18 21:00:00
categories: 架构
---



#### Tomcat安装与配置

1. 官网下载Tomcat，解压，复制两份。因为是在一台主机上模拟，所以主机上需要有多个Tomcat。

2. 配置修改Tomcat的配置文件：apache-tomcat/conf/server.xml，主要修改下面三处的端口号，防止两个Tomcat使用端口冲突。注意：两个Tomcat中端口不能重复

   ```xml
   <Server port="65004" shutdown="SHUTDOWN">

   <Connector port="65005" protocol="HTTP/1.1"
                  connectionTimeout="20000"
                  redirectPort="8443" />
                  
   <Connector port="65006" protocol="AJP/1.3" redirectPort="8443" />
   ```

3. 配置Tomcat的 JDK路径，在文件apache-tomcat/bin/catalina.sh中添加下面配置

   ```shell
   CATALINA_HOME=/root/apache-tomcat-7   #tomcat的路劲
   JAVA_HOME=/root/java/jdk1.7           #JDK安装路劲
   JRE_HOME=/root/java/jdk1.7/jre		  #jre路径
   ```

4. 测试Tomcat，进入apache-tomcat/bin/文件目录下执行

   ```shell
   sudo ./startup.sh
   ```

   在浏览器中输入localhost:65005 能显示出Tomcat主页，说明配置成功。

#### Nginx的安装与配置

1. 安装命令

   ```shell
   sudo apt-get install nginx
   ```

   如果安装不成功，可能是缺少依赖，将缺少的依赖安装即可。

   **Nginx的启动**

   ```shell
   nginx   #直接输入nginx 就能启动
   ```

   **Nginx的关闭**

   ```shell
   从容停止 : Kill -QUIT 13421 
   快速停止 : kill -TERM 13421 或 kill -INT 13421 
   强制停止 : pkill -9 nginx
   ```

2. ngnix 配置，修改配置文件/etc/ngnix/nginx.conf

   **进程数与每个进程的最大连接数**

   ```shell
   #工作进程个数，一般跟服务器cpu核数相等，或者核数的两倍
   worker_processes 2;

   #单个进程最大连接数
   events{
       worker_connections 1024; 
   }
   ```

   * nginx进程数，建议设置为和服务器cup核数相等，或者是核数的两倍
   * 单个进程最大连接数，该服务器的最大连接数=连接数*进程数； 服务器支持最大并发数=(连接数*进程数) /2 ，因为反向代理是双向的。

   **Nginx的基本配置**

   ```shell
   #nginx基本配置
   server{
       listen 8088;    #端口号
       server_name 192.168.22.227; #服务名
   }
   ```

   * 监听端口一般都为http端口：80;可以修改为其他，这里修改为8088。
   * server_name ：默认为localhost ，这里修改为服务器ip地址。

   **负载均衡列表基本配置**

   ```shell
       #服务器集群
       upstream zyhzm.me{
           #这里添加的是上面启动好的两台Tomcat服务器
            server 192.168.22.229:8080 weight=1;
            server 192.168.22.230:8080 weight=1;
       }

   location /{
           #将访问请求转向至服务器集群,zyhzm.me和上面upstream zyhzm.me 对应
           proxy_pass http://zyhzm.me;
           # 真实的客户端IP
           proxy_set_header   X-Real-IP        $remote_addr; 
           # 请求头中Host信息
           proxy_set_header   Host             $host; 
           # 代理路由信息，此处取IP有安全隐患
           proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
           # 真实的用户访问协议
           proxy_set_header   X-Forwarded-Proto $scheme;
       }
   ```

   ​

   **完整的配置文件示例**

   ```shell
   user nobody;
   #工作进程个数，一般跟服务器cpu核数相等，或者核数的两倍
   worker_processes 2;

   #单个进程最大连接数
   events{
       worker_connections 1024; 
   }

   http{

       keepalive_timeout 65;
       gzip on;
       #服务器集群
       upstream mycluster{
            #集群有几台服务器即可配置几台，weight表示权重，权重越大被访问到的几率越大
           #这里添加的是上面启动好的两台Tomcat服务器
            server 192.168.22.229:8080 weight=1;
            server 192.168.22.230:8080 weight=1;
       }

       #nginx基本配置
       server{
           listen 8088;    #端口号
           server_name 192.168.22.227; #服务名
           location /{
               #将访问请求转向至服务器集群,mycluster和上面upstream mycluster 对应
               proxy_pass http://mycluster;
               # 真实的客户端IP
               proxy_set_header   X-Real-IP        $remote_addr; 
               # 请求头中Host信息
               proxy_set_header   Host             $host; 
               # 代理路由信息，此处取IP有安全隐患
               proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
               # 真实的用户访问协议
               proxy_set_header   X-Forwarded-Proto $scheme;
           }

           error_page   500 502 503 504  /50x.html;  
           location = /50x.html {  
               root   html;  
           }  

       }
   }
   ```