---
title: Nginx 学习笔记
date: 2021-01-23 23:33:00
tags: 服务器、nginx
---

#### **Nginx 学习笔记**

总结
1 、 Nginx 简介
（ 1 ） 什么是Nginx 和可以做什么事情
（ 2 ） 正向代理
（ 3 ） 反向代理
（ 4 ） 动静分离

2 、 在Linux中安装nginx

3 、 Nginx的常用命令和配置文件

4 、 Nginx配置实例 1 反向代理

5 、 Nginx配置实例 2 负载均衡

6 、 Nginx配置实例 3 动静分离

7 、 Nginx的高可用集群
（ 1 ） nginx配置主从模式
（ 2 ） nginx配置双主模式

# Nginx 的简介

##### 1 、什么是nginx

Nginx是高性能的HTTP和反向代理的服务器，处理高并发能力是十分强大的，能经受高负
载的考验,有报告表明能支持高达 50,000个并发连接数。

##### 2 、正向代理

（ 1 ）需要在客户端配置代理服务器进行指定网站访问，隐藏了客户端的IP地址

![image-20210109110252280](C:\Users\10615\AppData\Roaming\Typora\typora-user-images\image-20210109110252280.png)

##### 3 、反向代理

服务器配置代理服务器，暴露的是代理服务器地址，隐藏了真实服务器IP地址。

![image-20210109110233040](C:\Users\10615\AppData\Roaming\Typora\typora-user-images\image-20210109110233040.png)

##### 4 、负载均衡

增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡

![image-20210109111630765](C:\Users\10615\AppData\Roaming\Typora\typora-user-images\image-20210109111630765.png)

##### 5 、动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速
度。降低原来单个服务器的压力。

> 不需要跟数据库交互获取的资源都是静态资源

![image-20210109113424433](C:\Users\10615\AppData\Roaming\Typora\typora-user-images\image-20210109113424433.png)

# Nginx 的安装

##### 1 、准备工作

（ 1 ）打开虚拟机，使用远程连接工具连接linux操作系统
（ 2 ）到nginx官网下载软件：http://nginx.org/

##### 2 、开始进行nginx安装

> 本来可以用 apt-get 的一起装的，但是想熟悉一下wget

（ 1 ）安装pcre依赖

1. 联网下载pcre压缩文件依赖

   ```
   wget http://downloads.sourceforge.net/project/pcre/pcre/8.37/pcre-8.37.tar.gz
   ```

2.  解压压缩文件

   ```
    tar –zxvf pcre-8.37.tar.gz
   ```

3. 执行`./configure`

4. 回到pcre目录下执行`make`，最后执行`make install`

5. 查看版本：

   ```
   pcre-config --version
   ```

（ 2 ）安装openssl 、zlib 、 gcc 依赖

```
yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
```

（ 3 ）安装nginx

1. 使用命令解压
2. ./configure
3. make && make install

进入目录 /usr/local/nginx/sbin/nginx 启动服务

> 在windows系统中访问linux中nginx，默认不能访问的，因为防火墙问题
> （ 1 ）关闭防火墙
> （ 2 ）开放访问的端口号， 80 端口

查看开放的端口号

```
firewall-cmd --list-all
```

设置开放的端口号

```
firewall-cmd --add-service=http – permanent
firewall-cmd --add-port=80/tcp --permanent
```

重启防火墙

```
firewall-cmd **–** reload
```

# Nginx 的常用的命令

进入nginx目录中
cd /usr/local/nginx/sbin

1 、查看nginx版本号

```
./nginx -v
或者nginx -v
```

2 、启动nginx

```
./nginx
service nginx start
```

3 、停止nginx

```
./nginx -s stop
```

4 、重新加载nginx

```
./nginx -s reload
```

# Nginx 的配置文件

##### 1 、nginx配置文件位置

```
cd /usr/local/nginx/conf/nginx.conf

使用apt-get安装时，配置文件在 /etc/nginx 中
```

##### 2 、配置文件中的内容

（ 1 ）全局块：配置服务器整体运行的配置指令

​			比如`worker_processes 1`：处理并发数的配置

（ 2 ）events块：影响 Nginx 服务器与用户的网络连接

​			比如`worker_connections 1024`： 支持的最大连接数为 1024

（ 3 ）http块
还包含两部分：
http全局块
server块

# Nginx 配置实例-反向代理实例 1

##### 1 、实现效果

（ 1 ）打开浏览器，在浏览器地址栏输入地址 **_[http://www.123.com](http://www.123.com)_** ，跳转到liunx系统tomcat主页面中

##### 2 、准备工作

（ 1 ）在liunx系统安装tomcat，使用默认端口 8080
* tomcat安装文件放到liunx系统中，解压
* 进入tomcat的bin目录中，./startup.sh启动tomcat服务器

### （ 2 ）对外开放访问的端口

```
firewall-cmd --add-port=80 80 /tcp --permanent
firewall-cmd **–** reload
```

### 查看已经开放的端口号

```
firewall-cmd --list-all
```

（ 3 ）在windows系统中通过浏览器访问tomcat服务器

##### 3 、访问过程的分析

![image-20210109115049027](C:\Users\10615\AppData\Roaming\Typora\typora-user-images\image-20210109115049027.png)

##### 4 、具体配置

第一步 在windows系统的host文件进行域名和ip对应关系的配置

（ 1 ）添加内容在host文件中

第二步 在nginx进行请求转发的配置（反向代理配置）

##### 5 、最终测试

# Nginx 配置实例-反向代理实例 2

##### 1 、实现效果

使用nginx反向代理，根据访问的路径跳转到不同端口的服务中
nginx监听端口为 9001 ，

访问 [http://192.168.17.129:9001/edu/](http://192.168.17.129:9001/edu/) 直接跳转到127.0.0.1:808 0
访问 [http://](http://) 192.168.17.129:9001/vod/ 直接跳转到127.0.0.1:808 1

##### 2 、准备工作

（ 1 ）准备两个tomcat服务器，一个 8080 端口，一个 8081 端口
（ 2 ）创建文件夹和测试页面

##### 3 、具体配置

（ 1 ）找到nginx配置文件，进行反向代理配置

（ 2 ）开放对外访问的端口号 9001 8080 8081

##### 4 、最终测试

# Nginx 配置实例-负载均衡

##### 1 、实现效果

（ 1 ）浏览器地址栏输入地址 [http://192.168.17.129/edu/a.html，负载均衡效果，平均](http://192.168.17.129/edu/a.html，负载均衡效果，平均) 8080
和 8081 端口中

##### 2 、准备工作

（ 1 ）准备两台tomcat服务器，一台 8080 ，一台 8081
（ 2 ）在两台tomcat里面webapps目录中，创建名称是edu文件夹，在edu文件夹中创建
页面a.html，用于测试

##### 3 、在nginx的配置文件中进行负载均衡的配置

##### 4 、nginx分配服务器策略

第一种 轮询（默认）
每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

第二种weight
weight代表权重默认为1,权重越高被分配的客户端越多

第三种ip_hash
每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器

第四种fair（第三方）
按后端服务器的响应时间来分配请求，响应时间短的优先分配。

# Nginx 配置实例-动静分离

##### 1 、什么是动静分离

![image-20210109115255261](C:\Users\10615\AppData\Roaming\Typora\typora-user-images\image-20210109115255261.png)

通过 location 指定不同的后缀名实现不同的请求转发。通过 expires 参数设置，可以使浏
览器缓存过期时间，减少与服务器之前的请求和流量。具体 Expires 定义：是给一个资源
设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，
所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件，
不建议使用 Expires 来缓存），我这里设置 3d，表示在这 3 天之内访问这个 URL，发送一
个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码 304 ，
如果有修改，则直接从服务器重新下载，返回状态码 200 。

##### 2 、准备工作

（ 1 ）在liunx系统中准备静态资源，用于进行访问

##### 3 、具体配置

（ 1 ）在nginx配置文件中进行配置

##### 4 、最终测试

（ 1 ）浏览器中输入地址：http://192.168.17.129/image/01.jpg

* 因为配置文件 autoindex on

（ 2 ）在浏览器地址栏输入地址：http://192.168.17.129/www/a.html

# Nginx 配置高可用的集群

##### 1 、什么是nginx高可用

![image-20210109115401695](C:\Users\10615\AppData\Roaming\Typora\typora-user-images\image-20210109115401695.png)

（ 1 ）需要两台nginx服务器
（ 2 ）需要keepalived
（ 3 ）需要虚拟ip

##### 2 、配置高可用的准备工作

### （ 1 ）需要两台服务器 192.168.17.129 和 192.168.17.

（ 2 ）在两台服务器安装nginx
（ 3 ）在两台服务器安装keepalived

##### 3 、在两台服务器安装keepalived

（ 1 ）使用yum命令进行安装
yum install keepalived **–** y

（ 2 ）安装之后，在etc里面生成目录keepalived，有文件keepalived.conf

##### 4 、完成高可用配置（主从配置）

（ 1 ）修改/etc/keepalived/keepalivec.conf配置文件

```
global_defs {
notification_email {
acassen@firewall.loc
failover@firewall.loc
sysadmin@firewall.loc
}
notification_email_from Alexandre.Cassen@firewall.loc
smtp_server 192.168.17.
smtp_connect_timeout 30
router_id LVS_DEVEL
}

vrrp_script chk_http_port {

script "/usr/local/src/nginx_check.sh"

interval 2 #（检测脚本执行的间隔）

weight 2

}

vrrp_instance VI_1 {
state BACKUP # 备份服务器上将 MASTER 改为 BACKUP
interface ens33 //网卡
virtual_router_id 51 # 主、备机的virtual_router_id必须相同
priority 90 # 主、备机取不同的优先级，主机值较大，备份机值较小
advert_int 1

authentication {
auth_type PASS
auth_pass 1111
}
virtual_ipaddress {
192.168.17.50 // VRRP H虚拟地址
}
}
```

（ 2 ）在/usr/local/src添加检测脚本

```
#!/bin/bash
A=`ps -C nginx –no-header |wc -l`
if [ $A -eq 0 ];then
 /usr/local/nginx/sbin/nginx
 sleep 2
 if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
 killall keepalived
 fi
fi
```

（ 3 ）把两台服务器上nginx和keepalived启动
启动nginx：./nginx
启动keepalived：systemctl start keepalived.service

##### 5 、最终测试

（ 1 ）在浏览器地址栏输入 虚拟ip地址 192.168.17.

（ 2 ）把主服务器（192.168.17.129）nginx和keepalived停止，再输入192.168.17.

## Nginx的原理

1 、mater和worker

2 、worker如何进行工作的

3 、一个master和多个woker有好处
（ 1 ）可以使用nginx **–** s reload 热部署，利用nginx进行热部署操作
（ 2 ）每个woker是独立的进程，如果有其中的一个woker出现问题，其他woker独立的，
继续进行争抢，实现请求过程，不会造成服务中断

4 、设置多少个woker合适

worker数和服务器的cpu数相等是最为适宜的

5 、连接数worker_connection
第一个：发送请求，占用了woker的几个连接数？
答案： 2 或者 4 个

第二个：nginx有一个master，有四个woker，每个woker支持最大的连接数 1024 ，支持的
最大并发数是多少？

- 普通的静态访问最大并发数是： worker_connections * worker_processes /2
- 而如果是HTTP作 为反向代理来说，最大并发数量应该是worker_connections *
  worker_processes/4。