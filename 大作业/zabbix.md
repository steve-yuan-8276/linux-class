# 负载均衡集群方案之zabbix监控（修订版）

## 写在前面

个人负责的部分主要是zabbix监控的部分，需求如下：

13 搭建zabbix监控告警系统，要求监控各个基础指标（cpu、内存、硬盘），网卡流量需要成图，还需要监控web站点的可用性；

14 定制自定义监控脚本，监控web服务器的并发连接数，超过100告警；

15 定制自定义监控脚本，监控mysql的队列，队列超过300告警；

16 定制自定义监控脚本，监控mysql的慢查询日志，每分钟超过60条日志需要告警，需要仔细分析慢查询日志的规律，确定日志条数；

基于上述需求，该部分笔记分为以下几步：

1. zabbix server安装及配置

2. zabbix client配置

3. web及mysql告警系统


## zabbix安装配置

根据试验分配的机器，一台有公网ip，剩下10台有内网ip，其中：

公网机器：47.98.190.43

zabbix-server：192.168.118.67


### 实验准备：

- 安装vim

```
//安装vim
yum install -y vim-enhanced

vi ~/.vimrc
//修改vim设置
set nu

set showmode

set ruler

set autoindent

syntax on

set tabstop=4
set shiftwidth=4
set expandtab
set smarttab

set hlsearch

autocmd InsertEnter * se cul

set shortmess=atI

//设置vi别名
vi ~/.bashrc
alias vi='/usr/bin/vim'

```

- 言环境变量设置

```
vi /etc/environment

//增加两行
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
```

- 更改主机名（目前iptables、firewalld都已禁用）

```
//建议给不同功能的虚拟机设置不同主机名，便于区别
hostnamectl set-hostname extranet
```

- 关闭防火墙

```
//关闭firewalld
systemctl stop firewalld.service
//禁止开机启动
systemctl disable firewalld.service

//安装iptables
yum install -y iptables-services

//编辑配置文件，重点是开放80、3306端口
vi /etc/sysconfig/iptables
# sample configuration for iptables service
# you can edit this manually or use system-config-firewall
# please do not ask us to add additional ports/services to this default configuration
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT       //开放80端口，针对httpd
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT     //开放3306端口，针对mysql
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
//重启防火墙使配置生效（未执行）
[root@stevey ~]# systemctl restart iptables.service
//设置防火墙开机启动（未执行）		
[root@stevey ~]# systemctl enable iptables.service
```

- 关闭selinux

```
vi /etc/selinux/config

//找到SELINUX=enforcing这一行，将enforcing 改为 disabled，保存退出并重启虚拟机
//此选项腾讯云默认关闭
```

- 安装epel拓展源

```
yum install -y epel-release
```

- 更新源

```
yum update
```

- 安装tree、wget

```
yum install -y tree wget
```

- 外网机器配置秘钥登陆

```
//创建.ssh目录并设置权限
mkdir -p .ssh
chmod 700 .ssh

//创建认证文件
touch authorized_key

//编辑认证文件
vi /root/.ssh/authorized_keys
//将本地机器的id_rsa.pub添加进去，多人共用的时候记得添加注释

//授权认证文件
chmod 600 authorized_keys

//修改ssh配置文件：
vi /etc/ssh/sshd_config

//将下面的内容的前面的 # 去掉
# PubkeyAuthentication yes
# AuthorizedKeysFile .ssh/authorized_keys

//然后，修改下面的内容的 yes 为 no 
PasswordAuthentication yes

//重启SSH服务
systemctl restart sshd
```

### 第一步：安装zabbix

- 这里采用的是yum安装，先安装zabbix拓展源，再利用yum安装zabbix。

```
//进入源码包安装文件夹，安装包都下载到这里方便管理
cd /usr/local/src

//下载安装源
wget repo.zabbix.com/zabbix/3.2/rhel/7/x86_64/zabbix-release-3.2-1.el7.noarch.rpm

//安装源
rpm -ivh zabbix-release-3.2-1.el7.noarch.rpm 

//安装zabbix
yum install -y zabbix-agent zabbix-get zabbix-server-mysql zabbix-web zabbix-web-mysql
```

**注：**zabbix会连带安装httpd和php，如果本机已经安装了nginx，80端口就会被占用，后面需要在配置文件中设置一下。

- 修改zabbix配置文件

```
vi /etc/zabbix/zabbix_server.conf
//修改以下配置
ListenPort=10051
LogFile=/var/log/zabbix/zabbix_server.log
LogFileSize=1
PidFile=/var/run/zabbix/zabbix_server.pid
DBName=zabbix
DBUser=zabbix
DBPassword=steve-zabbix
DBSocket=/tmp/mysql.sock
DBPort=3306
StartPollers=10
StartIPMIPollers=5
StartPollersUnreachable=5
StartTrappers=5
StartPingers=5
StartDiscoverers=5
CacheSize=8M
Timeout=4
LogSlowQueries=3000
```

- 保存后启动zabbix

```
systemctl start zabbix
```

### 第二步：配置nginx

这台机器是yum安装的nginx，版本号为1.12

- 先修改httpd配置文件

```
vi /etc/httpd/conf/httpd.conf

//搜索listen，将端口改为8080

保存之后重启httpd

systemctl start httpd
```

![](https://farm1.staticflickr.com/923/43250128332_aed17ca4da_o.png)

- 确认是否启动

![](https://farm1.staticflickr.com/918/43300051811_d9d0878022_o.png)

- 修改nginx配置文件 /etc/nginx/conf.d/*.conf;

```
vi /etc/nginx/conf.d/zabbix.conf 

//加入以下内容

server
{
    listen 80;
    server_name 172.16.155.120;

    location /
    {
        proxy_pass  http://172.16.155.132:8080;
        proxy_set_header Host   $host;
        proxy_set_header X-Real-IP  $remote_addr;
        proxy_set_header X-Forwarded-For  $proxy_add_x_forwarded_for;
    }
}
```

- 启动nginx

```
nginx -t

nginx -s reload
```

- 校验状态

![](https://farm1.staticflickr.com/837/43300351651_fc9a747ccb_o.png)

### 第三步：配置 mysql

```
vi /etc/my.cnf

//增加如下配置内容：
character_set_server = utf8

//保存后重启服务
systemctl restart mysqld

//进入数据库
mysql -uroot -S /tmp/mysql.sock -p123456

//以下在mysql中操作，创建zabbix库
mysql> create database zabbix character set utf8;

//创建用户
mysql> grant all on zabbix.* to 'zabbix'@'127.0.0.1' identified by 'admin-123';

//以下操作导入数据
//首先进入server文件夹
[root@local-linux02 src]# cd /usr/share/doc/zabbix-server-mysql-3.2.11/
[root@local-linux02 zabbix-server-mysql-3.2.11]# ls
AUTHORS  ChangeLog  COPYING  create.sql.gz  NEWS  README

//解压压缩包
gzip -d create.sql.gz 

//导入数据
mysql -uroot -p123456 zabbix < create.sql 

//修改zabbix-server配置
vi /etc/zabbix/zabbix_server.conf
//修改以下内容
DBHost=127.0.0.1     //在DBname上面添加
DBPassword=admin-123   //在DBuser下面增加

//启动 zabbix-server服务
systemctl start zabbix-server
systemctl enable zabbix-server
```

### 第四步：配置web界面

在浏览器输入`172.16.155.120/zabbix`,按照提示点击下一步，这里遇到时区错误，解决方法是修改配置文件 `/etc/httpd/conf.d/zabbix.conf`,找到timezone，改为Asia/Shanghai，保存退出后,执行`systemctl restart httpd`重启httpd 即可。

![](https://farm2.staticflickr.com/1784/42582302304_81f9705286_o.png)

![](https://farm2.staticflickr.com/1764/41506857720_88b9c92359_o.png)

之后继续选择下一步，Database port是0，代表mysql的默认端口3306，密码是创建zabbix用户时的密码。

安装成功后，可以是使用用户名、密码登陆看看效果。

#### TroubleShooting: 502 Bad Gateway

![](https://farm2.staticflickr.com/1829/42413106795_2f72ac3fc7_o.png)

**解决思路：**

![](https://farm1.staticflickr.com/846/43267581582_7c9d5bfde5_o.png)

如上所示，连接数据库失败，`zabbix'@'localhost' (using password: YES)`，这里给出了提示，应该是配置文件搞错了。

修改zabbix配置文件,将`DBPassword`改为`admin-123` 之后重启zabbix。

![](https://farm2.staticflickr.com/1770/41524361680_197ef58387_o.png)

登陆后台，使用使用默认用户名admin 密码zabbix

![](https://farm1.staticflickr.com/920/41524397330_49367ca38a_o.png)

修改系统设置更改admin登陆密码

![](https://farm2.staticflickr.com/1821/43334010311_151f72c7de_o.png)

修改完成后，点击update 刷新就可以生效

![](https://farm2.staticflickr.com/1823/42430332625_d4e417e4f7_o.png)

服务器端的安装暂告段落，下面开始配置客户端。

### 第五步：agent客户端配置

- 安装客户端

```
//下载yum源
wget repo.zabbix.com/zabbix/3.2/rhel/7/x86_64/zabbix-release-3.2-1.el7.noarch.rpm

//安装yum源
rpm -ivh zabbix-release-3.2-1.el7.noarch.rpm

//安装
yum install -y zabbix-agent
```

- 编辑配置文件

```
vi /etc/zabbix/zabbix_agentd.conf
//修改一下内容
Server=127.0.0.1修改为Server=172.16.155.120 //定义服务端的ip（被动模式）
ServerActive=127.0.0.1修改为172.16.155.120 //定义服务端的ip（主动模式）
Hostname=Zabbix server修改为Hostname=zabbix-agent //这是自定义的主机名，一会还需要在web界面下设置同样的主机名
```

- 启动服务

```
systemctl start zabbix-agent
 
systemctl enable zabbix-agent
```

![](https://farm2.staticflickr.com/1806/42430561855_01692d0240_o.png)

到这里配置基本完成。

最后补充一点，如果忘记admin密码怎么办？修改密码的方法如下：

server端进入mysql命令行进行操作

```
mysql -uroot -p

mysql> desc users;

mysql> update users set passwd=md5('steve-123') where alias='Admin';

select * from users;
```

完成之后，重新登陆验证一下是否成功。


## zabbix使用

### 添加监控主机

将需要监控的主机加入监控中心，就可以监控cpu、磁盘、内存及网络等情况了。

实现步骤：

#### 第一步：添加主机群组

- 依次点击：配置-主机群组-创建主机群组-设置组名

![](https://farm1.staticflickr.com/835/43286328152_9f8ef06de3_o.png)

- 根据提示，输入组名 zabbix-test-groups（名字随便起），之后点击add添加

![](https://farm1.staticflickr.com/926/41526848140_1e7655634b_o.png)

#### 第二步：添加主机

- 依次点击：配置-主机-创建主机

![](https://farm2.staticflickr.com/1821/29466514828_ab8364d1c7_o.png)

- 按照提示填写

![](https://farm2.staticflickr.com/1765/42619211794_841b95739a_o.png)

![](https://farm2.staticflickr.com/1786/43336608991_bcb48a67e3_o.png)

这里涉及的几个参数：

* 应用集 `Applications`：应用集就是多个监控项的组合，比如CPU相关的应用集、内存相关的应用集，应用集里面有具体的监控项；

* 监控项 `items`：监控项就是要监控的项目，比如内存使用、cpu等；

* 触发器 `triggers`：触发器是针对某个监控项做的告警规则，比如磁盘使用量超过80%就触发了告警规则，然后就告警；

* 图形 `graphs`：监控报表显示成图形；

* 自动发现 `discovery`：zabbix特有的一个机制，会自动地去发现服务器上监控项目，比如网卡浏览就可以自动发现网卡设备并监控起来；

* web监测 `web`：监控指定网站的某个URL访问是否正常，比如状态码是否为200，或者访问时间是否超过某个设定的时间段。

### 主动模式和被动模式

* `被动模式`，客户端开一个端口默认10050,等待服务端来取数据,然后客户端收集数据发送到服务端后结束；

* `主动模式`，客户端每隔一段时间主动向服务端发起连接请求-->服务端收到请求,查询客户端需要取的item信息,发送给客户端-->客户端收集数据发送服务端-->结束

这里的主动或被动都是针对客户端而言，两种模式各有特点：

1. 当客户端数量非常多时，建议使用主动模式，这样可以降低服务端的压力；

2. 被动模式需要客户端开一个listen端口等待服务端来拿数据,那么如果这个被监控的机器处在防火墙或是在内网中,不映射端口,服务端是没办法发送数据到这个客户端的,这时只能用主动模式；


#### 被动模式配置

zabbix监控模板默认使用的就是被动模式，server端不需要专门配置，直接添加host

agent配置如下：

```
vi /etc/zabbix/zabbix_agentd.conf

PidFile=/var/run/zabbix/zabbix_agentd.pid
LogFile=/var/log/zabbix/zabbix_agentd.log
LogFileSize=0
StartAgents=3
Server=172.16.155.120
ServerActive=172.16.155.120
Hostname=zabbix_client
HostMetadataItem=system.uname
```

#### 主动模式配置：

##### agent配置如下：

```
vi /etc/zabbix/zabbix_agentd.conf 

PidFile=/var/run/zabbix/zabbix_agentd.pid
LogFile=/var/log/zabbix/zabbix_agentd.log
LogFileSize=0
StartAgents=0
ServerActive=172.16.155.120
Hostname=zabbix_master
Include=/etc/zabbix/zabbix_agentd.d/*.conf
```

- 重启zabbix：

```
systemctl restart zabbix-agent
```

##### server端配置：

克隆一个模板，把所有的类型改为Zabbix agent(Active)主动模式：

克隆模板：Configuration–》Template–》Template OS Linux（选择需要克隆的模板）–》Full clone（最下面）–》Template name：Template OS Linux Active–》Add

删除软链接模版，添加一个新的模版，克隆类型为active。另外，克隆模板成功后，记得修改类型，改为主动模式。
                               
### 添加自定义模板

我们可以自定义一个常用模板，方便给新增主机添加监控项目。

#### 第一步：新建模版

- 依次点击：配置-模版-创建模版，就可以创建自己的模版了，按照要求填写模版名称、可见名称、群组，最后点击添加按钮，就创建了一个自己的模版。

![](https://farm1.staticflickr.com/833/28467727047_a724a1cf13_o.png)

- 按照提示填写后，点击apply

![](https://farm2.staticflickr.com/1829/43286741782_541bb643a9_o.png)

#### 第二步：添加监控项

截至目前，我们自定义的模版是空白的，我们可以将需要的监控项从已有的模版当中复制过来添加进去。

##### 复制监控项

具体来说，找到相应的模版，点击监控项，在对应的监控项左侧方框打勾选中，之后点击复制，目标类型选择模版，在弹出列表中选择之前我们自定义的模版，选中后点击复制即可。

- 这里从`templates os linux`这个系统自带模版中复制出几个items来做实验。

![](https://farm1.staticflickr.com/923/43286825542_0c4834afc6_o.png)

- 找到templates os linux双击打开，点击items，下拉就可以看到都监控哪些项目，选中以下项目，点击copy

![](https://farm2.staticflickr.com/1826/28467911147_0c1a8ace09_o.png)

![](https://farm1.staticflickr.com/840/41527398620_074acaf389_o.png)

- 这样就添加好了，依次点击configuration、templates、items，可以看到刚才复制的4个监控项已经在这里了。

![](https://farm1.staticflickr.com/838/43337380321_c44e9cac63_o.png)

- 同时，还可以在模版中添加触发器。所谓触发器，就是trigger，当达到某种条件，就会触发影响的操作。

- 依次点击triggers，create triggers 创建触发器，名称填写`{HOST.NAME}1分钟负载（每核）`，点击添加按钮，按照提示设置条件、监控项、群组、主机，

![](https://farm2.staticflickr.com/1801/28468489377_26c297e91f_o.png)

![](https://farm2.staticflickr.com/1769/43287429902_ac676072bc_o.png)

- 点击，在弹出页面设置条件，监控项选择`Processor load (1 min average per core)` ， functions设置为 `最新的T值> N`,下面的N设置为2.之后点击插入，回到触发器界面，点击添加，就成功设置了一个触发器。

##### 链接监控项

- 先删除之间复制的监控项

![](https://farm1.staticflickr.com/845/28468560367_e9bf1ab259_o.png)

- 按提示进行操作添加链接模版。需要注意的是，图片当中有一点忘记标注了，选择模版后，必须点击add，之后在点击update生效。

![](https://farm1.staticflickr.com/839/43337707871_8a3b940cb6_o.png)

- 然后，在steve-temp模版items界面，删除不需要的监控项就OK。

- 如果需要删除，还是回到linked templates界面，选择unlink and clear，之后点击updates即可。

![](https://farm2.staticflickr.com/1809/28468803387_3fb542cf37_o.png)


### 处理图形中的乱码 （服务端操作)

当系统界面设置为中文后，由于zabbix缺少中文字体，会出现无法正确显示的现象。

![](https://farm2.staticflickr.com/1826/43251996722_1dd538381e_o.jpg)

解决思路：zabbix的字体文件保存在`/usr/share/zabbix/fonts/`，文件名为`graphfont.ttf`,只要拷贝一个中文字体，替换原来的字体即可。

- 第一步：从网上下载一个中文字体或者从系统文件中拷贝一个中文字体，比如简体仿宋`simfang.ttf`，通过rsync或者ftp上传到虚拟机`/usr/local/src` 目录下。

- 第二步：备份字体文件

```
cd /usr/share/zabbix/fonts/

mv graphfont.ttf graphfont.ttf.bak
```

- 第三步：制作软链接

```
ln -s simfang.ttf graphfont.ttf
```

- 最后，刷新浏览器即可。

### 自动发现

- 依次点击：configuration-->discovery-->create discovery rule

![](https://farm2.staticflickr.com/1766/43338033591_cdcb4de32d_o.png)

- 填写信息，其他信息随便写，最关键的是监控网段

![](https://farm2.staticflickr.com/1787/41528665130_9b12875e38_o.png)

- 重启zabbix

```
//重启服务端
systemctl restart zabbix-server

//重启客户端
systemctl restart zabbix-agent
```

- 更新之后图形会出现网卡的流量图形

![](https://farm2.staticflickr.com/1806/29431899638_bfca47dffe_o.jpg)

### 添加自定义监控项目

需求：监控某台web服务器的80端口并发连接数，并设置图形。

**实现步骤：**

- 在zabbix-agent端编辑自定义脚本

```
vim /usr/local/sbin/estab.sh

编辑脚本内容如下：

#!/bin/bash
##获取80端口并发连接数
netstat -ant |grep ':80 ' |grep -c ESTABLISHED
```

- 修改脚本权限

```
chmod 755 /usr/local/sbin/estab.sh
```

- 在zabbix-agent端编辑配置文件，定义监控的key

```
vim /etc/zabbix/zabbix_agentd.conf 

//增加如下配置内容：
UnsafeUserParameters=1  
//使用自定义脚本
//自定义监控项的key为my.estab.count，后面的[*]里面写脚本的参数，如果没有参数则可以省略，脚本为/usr/local/sbin/estab.sh
UserParameter=my.estab.count[*],/usr/local/sbin/estab.sh 
```

- 重启zabbix-agent服务

```
systemctl restart zabbix-agent
```

- 服务端验证脚本

```
zabbix_get -s 172.16.111.110 -p 10050 -k 'my.estab.count'
```

- 然后在zabbix监控中心（浏览器）配置增加监控项目

![](https://farm2.staticflickr.com/1825/43272341312_21e6259c7a_o.jpg)

- 键值写my.estab.count

![](https://farm2.staticflickr.com/1786/41512311190_da86fccafc_o.jpg)

- 添加该项目后，到“监测中”——“最新数据”查看刚添加的项目是否有数据出现，有了数据就可以添加图形了，“配置”——“主机” ——“图形” ——“创建图形”

![](https://farm2.staticflickr.com/1783/43321862171_3ef6bd6e4e_o.jpg)

### 配置邮件告警

#### 第一步：QQ邮箱设置

登录你的qq邮箱，设置开启POP3、IMAP、SMTP服务，开启并记录授权码。

#### 第二步：到监控中心设置邮件告警

- 依次点击“管理”-“报警媒介类型”-“创建媒体类型”

脚本参数：

* `{ALERT.SENDTO}` ：表示接收邮件地址

* `{ALERT.SUBJECT}` ：主题

* `{ALERT.MESSAGE}` ：邮件内容

![](https://farm1.staticflickr.com/924/41512366510_1bf8934a4b_o.jpg)

#### 第三步：创建脚本，定义脚本路径（服务端）


- 确定告警邮件脚本应该放到哪个位置下

```
grep 'AlertScriptsPath=' /etc/zabbix/zabbix_server.conf
```

- 按需求修改脚本内容

```
vim /usr/lib/zabbix/alertscripts/mail.py

//增加脚本以下内容：

#!/usr/bin/env python
#-*- coding: UTF-8 -*-
import os,sys
reload(sys)
sys.setdefaultencoding('utf8')
import getopt
import smtplib
from email.MIMEText import MIMEText
from email.MIMEMultipart import MIMEMultipart
from  subprocess import *
def sendqqmail(username,password,mailfrom,mailto,subject,content):
    gserver = 'smtp.qq.com'
    gport = 25
    try:
        msg = MIMEText(unicode(content).encode('utf-8'))
        msg['from'] = mailfrom
        msg['to'] = mailto
        msg['Reply-To'] = mailfrom
        msg['Subject'] = subject
        smtp = smtplib.SMTP(gserver, gport)
        smtp.set_debuglevel(0)
        smtp.ehlo()
        smtp.login(username,password)
        smtp.sendmail(mailfrom, mailto, msg.as_string())
        smtp.close()
    except Exception,err:
        print "Send mail failed. Error: %s" % err
def main():
    to=sys.argv[1]
    subject=sys.argv[2]
    content=sys.argv[3]
//定义QQ邮箱的账号和密码（请勿把真实的用户名和密码放到网上公开，否则你会死的很惨）
    sendqqmail('1234567@qq.com','aaaaaaaaaa','1234567@qq.com',to,subject,content)
if __name__ == "__main__":
    main()
```

**脚本使用说明**
1. 首先定义好脚本中的邮箱账号和密码
2. 脚本执行命令为：python mail.py 目标邮箱 "邮件主题" "邮件内容"

- 更改权限

```
chmod 755 /usr/lib/zabbix/alertscripts/mail.py
```

- 测试邮件发送

```
python /usr/lib/zabbix/alertscripts/mail.py aaa@qq.com "username" "password"
```

#### 第四步：创建一个接受告警邮件的用户

- 依次“管理”，“用户”，“创建用户”，“报警媒介”，类型选择“baojing”，注意用户的权限，如果没有需要到用户组去设置权限。

![](https://farm2.staticflickr.com/1783/42417999745_70114bc5c2_o.jpg)

#### 第五步：设置动作

- 依次点击“配置”，“动作”，“创建动作”，名称写“sendmail”（自定义），“操作”页面复制如下内容：

```
HOST:{HOST.NAME} {HOST.IP}
TIME:{EVENT.DATE}  {EVENT.TIME} 
LEVEL:{TRIGGER.SEVERITY} 
NAME:{TRIGGER.NAME}
messages:{ITEM.NAME}:{ITEM.VALUE}
ID:{EVENT.ID}
```

- 切换到“恢复操作”，把信息改成如下：

```
HOST:{HOST.NAME} {HOST.IP}
TIME:{EVENT.DATE}  {EVENT.TIME} 
LEVEL:{TRIGGER.SEVERITY} 
NAME:{TRIGGER.NAME}
messages:{ITEM.NAME}:{ITEM.VALUE}
ID:{EVENT.ID}
```

- “操作”，选择发送的用户为刚创建的用户，仅送到选择“baojing”；

- 点击“新的”，“操作”，选择发送的用户为刚创建的用户，仅送到选择“baojing”

#### 测试告警

仪表板出现报错信息，邮件发送成功

![](https://farm2.staticflickr.com/1789/43272585342_e1c89d4ec0_o.jpg)

## Zabbix 监控mysql篇

### 第一步：zabbix安装

安装

```
rpm --import http://repo.zabbix.com/RPM-GPG-KEY-ZABBIX

rpm -ivh http://repo.zabbix.com/zabbix/3.4/rhel/7/x86_64/zabbix-release-3.4-1.el7.centos.noarch.rpm

yum install -y zabbix-agent
```

修改zabbix agent的配置

```
sudo vi /etc/zabbix/zabbix_agentd.conf

Server=172.16.155.120
ServerActive=172.16.155.120
Hostname=dbmysql
```

启动zabbix agent服务并设置开机自启

```
sudo systemctl start zabbix-agent.service
sudo systemctl enable zabbix-agent.service
```

### 第二步：在server端添加监控主机

#### 添加主机群组及主机

* 创建mysql的主机群组；

* 添加MySQL master的主机dbmysql；


#### 创建模板（两种方式）

##### 直接从其他模板复制

添加db_monitor的模板：

1. 找到Template App MySQL，选择监控项


2. 点击复制，复制到db_monitor

复制途中两个打钩的监控项（一个是慢查询、一个是MySQL的状态），点击复制，



##### 链接到模板

在链接指示器中选择Template App MySQL（下图是已经添加过监控项的）：

可以看到已经有了1个应用集、14个监控项等等。

点击取消链接：


至此，模板已经初步创建好了。

#### 添加自定义的监控项到模板

在客户端开启自定义监控脚本。修改客户端的配置文件zabbix_agent.conf，加入过修改以下内容：

```
UnsafeUserParameters=1
UserParameter=slow.query.count[*],/usr/local/sbin/zabbix/slow_query_count.sh
```

### 告警脚本

#### 监控web服务器的并发连接数，超过100告警

```
vi /opt/nginx_log_monit.sh

#!/bin/bash
#日志文件
logfile=/usr/local/nginx/logs/access.log
   
#开始时间
start_time=`date -d"$last_minutes minutes ago" +"%H:%M:%S"`
   
#结束时间
stop_time=`date +"%H:%M:%S"`
   
#过滤出单位之间内的日志并统计最高ip数
tac $logfile | awk -v st="$start_time" -v et="$stop_time" '{t=substr($4,RSTART+14,21);if(t>=st && t<=et) {print $0}}' \
| awk '{print $1}' | sort | uniq -c | sort -nr > /root/log_ip_top10
ip_top=`cat /root/log_ip_top10 | head -1 | awk '{print $1}'`
# 单位时间[1分钟]内单ip访问次数超过100次，则触发邮件报警
if [[ $ip_top -gt 100 ]];then
 /usr/bin/python /opt/send_mail.py &
fi
```

python邮件脚本

```
/opt/send_mail.py
# -*- coding: utf-8 -*-
from email import encoders
from email.header import Header
from email.mime.text import MIMEText
from email.utils import parseaddr, formataddr
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from datetime import datetime
import os
import smtplib
def _format_addr(s):
 name, addr = parseaddr(s)
 return formataddr((Header(name, 'utf-8').encode(), addr))
# 邮箱定义
smtp_server = 'smtp.163.com'
smtp_port = 465
from_addr = 'yuanf8276@163.com'
password = os.environ.get('monit@123')
to_addr = ['yuanf8276@163.com']
# 邮件对象
msg = MIMEMultipart()
msg['From'] = _format_addr('发件人 <%s>' % from_addr)
msg['To'] = _format_addr('收件人 <%s>' % to_addr)
msg['Subject'] = Header('Warning:单ip请求次数异常', 'utf-8').encode()
# 获取系统中要发送的文本内容
with open('/root/log_ip_top10', 'r') as f:
 line = f.readline().strip()
 line = line.split(" ")
print(line)
# 邮件正文是MIMEText:
html = '<html><body><h2>一分钟内单ip请求次数超过阀值</h2>' + \
 '<p>ip:%s  请求次数/min:%s</p>' % (line[1],line[0]) + \
 '</body></html>'
msg.attach(MIMEText(html, 'html', 'utf-8'))
server = smtplib.SMTP_SSL(smtp_server, smtp_port)
server.login(from_addr, password)
server.sendmail(from_addr, to_addr, msg.as_string())
server.quit()
```

- 定时任务

```
crontab -e
* * * * * /bin/bash -x /opt/nginx_log_monit.sh >/dev/null 2>&1
```


#### mysql队列数超过300报警

```
vim /usr/local/sbin/mysql.sh
#!/bin/bash  
#堵塞最大数量  
maxNum=300  
#接收者  
email_reciver="yuanf8276@163.com" 
#smtp服务器地址  
email_smtphost=smtp.exmail.126.com  
#发送者邮箱  
email_sender=yuanf8276@126.com  
#邮箱用户名  
email_username=tinna_wu
#使用qq邮箱进行发送需要注意：首先需要开启：POP3/SMTP服务。  
email_password=xxxxx
#服务器ip  
local_ip=`ifconfig|grep inet|awk '{print $2}'|awk -F " " '{print $1}'|head -1`  
#主题  
email_title="服务器${local_ip}消息队列堵塞"  
#Mysql的环境变量  
export RABBITMQPATH=/usr/lib/rabbitmq/bin/  
#获取所有队列的名字和每个队列中的消息数量，存入'queueNum'数组中  
declare -A A queue Json   
queuelindex=0  
for QUEUE in $(rabbitmqctl list_queues |grep -v 'Listing queues ...' | awk -F' ' '{print $1}');  
do   
    #统计每个消息队列的数量  
    queueJson[$QUEUE]=$(rabbitmqctl list_queues |grep $QUEUE | awk -F' ' '{print $2}')   
    nums=${queueJson[$QUEUE]}  
    # -ge    
    if [[ $nums -ge $maxNum ]]; then  
        #存key  
        queueName[$queueIndex]=$QUEUE  
        queueIndex=`expr $queueIndex + 1`  
    fi  
done   
#如果有异常,发送邮件  
exceptionNum=${#queueName[@]}  
if [[ $exceptionNum -gt 0 ]]; then  
    #有队列阻塞，exceptionName存放的为堵塞队列的名称,发送邮件  
    #内容  
    email_content="队列阻塞情况:"  
    for name in ${queueName[*]}  
    do  
        email_content=$email_content"\\n${name}:${queueJson[${name}]}"  
    done  
    echo "##count at $(date +'%d-%m-%Y %H:%M:%S') ##"  
    echo -e $email_content  
    #发送邮件  
    /usr/local/sbin/mysql.sh -f ${email_sender} -t ${email_reciver} -s ${email_smtphost} -u ${email_title} -xu ${email_username} -xp ${email_password} -m ${email_content} -o message-charset=utf-8  
fi  
```

定时任务

```
crontab -e

*/30 * * * * root sh /usr/local/sbin/mysql.sh
```

#### 慢查询日志超过60条/分钟告警

```
vim /usr/local/sbin/zabbix/slow_query_count.sh

#!/bin/bash
# script name: slow_query_count.sh

slow_log=/data/mysql/dbm-slow.log
grep 'timestamp' $slow_log | awk -F '=' '{print $2}' |awk -F ';' '{print $1}'|sort -r > /tmp/timestamp.log
now=`sed -n '1'p /tmp/timestamp.log`
alert_num=60
one_min_ago=$[$now-60]
num=0


# 判断两个时间戳之前的差值
slow_query_sum() {
    if [ $1 -ge $2 ]
    then
        num=$[$num+1]
    else
	break
    fi
}


# 获取在一分钟内的慢查询数

for n in `cat /tmp/timestamp.log`
do
    slow_query_sum $n $one_min_ago
done

# 删除临时文件
rm -f /tmp/timestamp.log
# There are $num slow query in a minute.
echo $num
```

定时任务

```
crontab -e

* * * * * root sh /usr/local/sbin/zabbix/slow_query_count.sh
```


./configure \
--prefix=/usr/local/php \
--with-apxs2=/usr/local/apache2.4/bin/apxs \
--with-config-file-path=/usr/local/php/etc \
--enable-inline-optimization \
--disable-debug \
--disable-rpath \
--enable-shared  \
--enable-soap \
--with-libxml-dir \
--with-xmlrpc \
--with-openssl \
--with-mhash \
--with-pcre-regex \
--with-sqlite3 \
--with-zlib \
--enable-bcmath \
--with-iconv \
--with-bz2 \
--enable-calendar \
--with-curl \
--with-cdb \
--enable-dom \
--enable-exif \
--enable-fileinfo \
--enable-filter \
--with-pcre-dir \
--enable-ftp \
--with-gd \
--with-openssl-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib-dir  \
--with-freetype-dir \
--enable-gd-jis-conv \
--with-gettext \
--with-gmp \
--with-mhash \
--enable-json \
--enable-mbstring \
--enable-mbregex \
--enable-mbregex-backtrack \
--with-libmbfl \
--with-onig \
--enable-pdo \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-zlib-dir \
--with-pdo-sqlite \
--with-readline \
--enable-session \
--enable-shmop \
--enable-simplexml \
--enable-sockets  \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-wddx \
--with-libxml-dir \
--with-xsl \
--enable-zip \
--enable-mysqlnd-compression-support \
--with-pear \
--enable-opcache




./configure --prefix=/usr/local/php --with-apr=/usr/local/apr --with-config-file-path=/usr/local/php/etc --enable-fpm --with-fpm-user=nginx  --with-fpm-group=nginx --enable-inline-optimization --disable-debug --disable-rpath --enable-shared  --enable-soap --with-libxml-dir --with-xmlrpc --with-openssl --with-mhash --with-pcre-regex --with-sqlite3 --with-zlib --enable-bcmath --with-iconv --with-bz2 --enable-calendar --with-curl --with-cdb --enable-dom --enable-exif --enable-fileinfo --enable-filter --with-pcre-dir --enable-ftp --with-gd --with-openssl-dir --with-jpeg-dir --with-png-dir --with-zlib-dir  --with-freetype-dir --enable-gd-jis-conv --with-gettext --with-gmp --with-mhash --enable-json --enable-mbstring --enable-mbregex --enable-mbregex-backtrack --with-libmbfl --with-onig --enable-pdo --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-zlib-dir --with-pdo-sqlite --with-readline --enable-session --enable-shmop --enable-simplexml --enable-sockets --enable-sysvmsg --enable-sysvsem --enable-sysvshm --enable-wddx --with-libxml-dir --with-xsl --enable-zip --enable-mysqlnd-compression-support --with-pear --enable-opcache --disable-fileinfo