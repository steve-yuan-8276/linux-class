# zabbix安装记录

结合着一个礼拜踩坑的记录，简单总结一下自己已经解决的问题和解决不了的问题，希望能对队友们有所帮助。

## 关于lamp环境：

由于测试的云主机配置很低，内存只有512M，而且只有内网环境。结合这一个礼拜的失败经验，我的感觉是lamp环境+zabbix，源码包安装。

之前还测试过lnmp环境+zabbix的方式，但是行不通，不知道问题在哪里，配置好之后无法访问，不是502，就是404，估计是权限设置出现了问题，检查过文件目录，也试过chmod更改权限，chown更改属主，都搞不定。

nginx当中php解析也不正常，自己新建的php文档可以，拷贝到同一目录下的zabbix.php就不行，不知道哪里出了问题。

另外，nginx反向代理也不会。

## myql安装

之前测试的mysql5.6,装上之后跑不起来，后来经队友指点才知道机器内存太小，解决方法是在mysql配置如下：

```
vim /etc/my.cnf

//修改以下配置
log_bin = root    //15行
basedir = /usr/local/mysql
datadir = /data/mysql
port = 3306
server_id = 128
socket = /tmp/mysql.sock

//去掉以下配置前面的的型号
innodb_buffer_pool_size = 128M     
join_buffer_size = 128M
sort_buffer_size = 2M
read_rnd_buffer_size = 2M

//针对小内存机器的优化配置
//之前因为没有添加以下配置，造成mysql无法正常启动
performance_schema_max_table_instances=400
table_definition_cache=400
table_open_cache=256
```

另外，后来队友又说mysql不用建在本机，放到mysql服务器就好，这个在系统配置文件中怎么设置，也不知道。

## 关于apache

相对而言，这个算是最简单的了，而且还可以yum安装。

## 关于php安装

跟mysql一样，编译安装的时候内存不足，增加虚拟内存之后才装上。
 
## 关于zabbix

首先yum安装用不了，按照课程中的方法，更新yum源，不仅装不了，而且装完之后，每次运行yum，都会试图去链接zabbix库，然后卡死在哪里。

源码包安装主要有以下问题：

### 一是要装一堆的依赖

```
1. configure: error: MySQL library not found MySQL library not found
yum install mysql-devel  

2. configure error: Invalid Net-SNMP directory - unable to find net-snmp-config
yum -y install net-snmp-devel
依然报这个错误，则安装libsnmp-dev
yum install libsnmp-devel

3. configure: error: unixODBC library not found
yum install unixODBC-devel

4. configure: error: SSH2 library not found
yum install libssh2

5. configure: error: Invalid OPENIPMI directory - unableto find ipmiif.h
yum install OpenIPMI-devel

6. configure: error: OpenSSL library libssl or libcryptonot found
yum install openssl-devel

7. configure: error: Curl library not found
yum install libcurl-devel

8. configure: error: LIBXML2 library not found
yum install libxml2-devel -y

9. configure: error: Unable to find "javac"executable in path
yum install java-devel -y

10. configure: error: Jabber library not found
yum install iksemel-devel -y（本机yum源没有，需要编译安装）

11. configure: error: Invalid Net-SNMP directory - unableto find net-snmp-config
yum install net-snmp net-snmp-devel

12. configure: error: Invalid LDAP directory - unable tofind ldap.h
yum install openldap-devel

13. configure: error: Unable to use libevent (libevent check failed)
yum install libevent-devel

14. Unable to use libpcre (libpcre check failed) 
yum install libpcre3-devel
```

iksemel-devely用yum安装不了，必须采用源码包安装

```
wget https://src.fedoraproject.org/lookaside/pkgs/iksemel/iksemel-1.4.tar.gz/532e77181694f87ad5eb59435d11c1ca/iksemel-1.4.tar.gz

//rsyncc同步过来之后解压编译安装
./configure --prefix=/usr/local/iksemel --enable-FEATURE --enable-dependency-tracking --enable-shared --enable-static --with-PACKAGE --with-gnu-ld

make && make install 
```

### 二是无法启动

![](https://farm1.staticflickr.com/928/43067430134_7b54ae2551_o.jpg)

解决思路：

关键是这一句：Linux error while loading shared libraries: cannot open shared object file: No such file or directory

找了一下午，最终在别人的提示下意识到是缺少共享库 `libiksemel.so.3`，如下：

```
[root@zabbix-server ~]# find / -name 'libiksemel.so.3'
/usr/local/iksemel/lib/libiksemel.so.3
/usr/local/src/iksemel-1.4/src/.libs/libiksemel.so.3
```

有人说只要做一个软链接就可以了，于是照做，问题依旧。后来猜想是不是软链接做的不对，检查了一下源文件，发现这也是个软链接文件。

```
[root@zabbix-server ~]# ls -ld /usr/local/iksemel/lib/libiksemel.so.3
lrwxrwxrwx 1 root root 19 Aug  1 15:36 /usr/local/iksemel/lib/libiksemel.so.3 -> libiksemel.so.3.1.1
```

既然如此，干脆复制这个文件到库文件好了。

```
[root@zabbix-server ~]# cp /usr/local/iksemel/lib/libiksemel.so.3 /usr/lib/

[root@zabbix-server ~]# cp /usr/local/iksemel/lib/libiksemel.so.3 /usr/lib64/
```

再次重启zabbix，终于正常启动了。

![](https://farm1.staticflickr.com/855/43783897191_6a34b30eaf_o.png)

### 三还是无法启动，PID文件不可读

```
::: Error :: pid file /var/run/zabbix/zabbix_server.pid not readable (yet )
```

解决方式:

创建zabbix文件夹以及zabbix_server.pid文件并赋予权限

```
mkdir -p /var/run/zabbix

chown zabbix:zabbix /var/run/zabbix/

touch /var/run/zabbix/zabbix_server.pid
```

四是无法访问zabbix文件，按照网上找到的方法，目录文件为/usr/local/nginx/zabbix/，把php文件放到该文件目录下。

```
/usr/local/src/zabbix-3.4.11/frontends/php/*  /usr/local/nginx/zabbix/
```

测试过以下两种操作，都没用。

```
chmod -R 777 /usr/local/nginx/zabbix/

chown -R nginx:nginx /usr/local/nginx/zabbix/
```

最奇怪的是，自己新建了一个1.html，可以访问；1.php，也可以访问。但就是无法放该目录下的其他文件。

因为无法访问，也就没有办法测试下面的浏览器安装部分，实验到此终止。

后来又测试了安装lamp环境，在安装php的时候也遇到内存不足的问题，安装不成功。基本上就这些。