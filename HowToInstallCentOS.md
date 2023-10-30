---
title: Linux折腾笔记：虚拟机安装Cent OS 7及SSH登陆设置
date: 2017-12-13 17:52:55
categories: linux
tags: [centos,ssh] 
---

### 准备工作：
#####  安装VMmare fusion
##### 下载 cent OS

 **下载地址：**
 
 官方镜像：[CentOS官网
](https://www.centos.org/download/) 
国内镜像：[搜狐](http://mirrors.sohu.com/centos/)、[163](http://mirrors.163.com/centos/)、[阿里巴巴](https://mirrors.aliyun.com/centos/)

##### 新建虚拟机：都是傻瓜式的，不要需要注意以下几点：

* 在“自定义硬件”的选项中，网络适配器建议选择NAT模式，兼容性最好。

* 在系统“安装位置”中，选择“我要配置分区”，自定义系统分区；在 LVM下拉菜单，选择 “标准分区”，之后点击加号手动进行修改，点击“添加挂载点”，最后点击“完成”，保存“接收更改”。推荐有以下是3个分区：

分区名称| 空间大小
---|---
/boot| 200M
/swap | 4G
/ | 剩余磁盘空间

另外，别忘记设置一个复杂一点的root密码。                 

<!--more-->

### 设置网络静态IP

#### 获取IP地址信息

在登录框中输入以下命令，获取ip地址

```
dhchlient    // 自动获取ip地址
```

可以通过ping命令来验证网络是否联通

```
ping -c 4 www.163.com
```

查看当前网络状况，使用命令

```
ip addr    // 查看ip地址信息
```
需要注意的是，这里需要记下来ip address、gateway、netmask、ensXX（网卡信息），后面设置静态ip会用到。

#### 编辑网卡配置

```
# vi /etc/sysconfig/network-scripts/ifcfg-ens33
```
注：此处的XX，就是前面查到的网卡信息。

```
ONBOOT=yes；//表示网卡随系统一起启动
BOOTPROTO=static // 用来设置网卡启动类型，dhcp表示自动获取ip地址，static表示手动设置ip地址
IPADDR=xxx.xxx.xxx.xxx  //dhclient获取的网址
NETMASK=255.255.255.0    //子网掩码
GATEWAEY=xxx.xxx.xxx.xxx     //网关地址
DNS1=119.29.29.29    //DNS服务器地址，这里提供的一个公共DNS
```

最后，先按“esc”退出编辑模式，之后再输入“:wq”进行保存。

#### 重启ip设置

```
systemctl restart network.service

```
正常情况下，静态ip就设置好。检查是否成功，可以ping一下外网，如：

```
ping -c 4 www.163.com
```

#### Troubleshooting

按照教程进行到这一部后发现网络不通，提示错误 `name or service not know` ，又是一番折腾。这样要注意的是：

* ip和网关必须是在同一个网段；

* DNS设置错误也会出错，如果这个DNS地址不行，就试试8.8.8.8

### 远程连接虚拟机（SSH连接）
#### 总体思路：

- 假设有两台电脑A和B，这里的A是你要操作的电脑；B是虚拟机。

- 先在A电脑生成密匙对（公钥、私钥各一个），私钥始终保存在A电脑不动，公钥复制出来，添加到电脑B的`authorized_ keys` 里面，再进行一些必要的设置，就可以从A电脑免密登陆B电脑了。

- 注意，如果已经在A电脑上设置过keygen，比如笔者已经在搞github折腾过一会，密匙对已经存在，就不用重新生成了，只要公钥复制出来粘贴过去就可以了。

#### 前提条件：

* mac自带终端或者item2

#### 输入如下命令进行连接：

```
ssh username@xxx.xxx.xxx.xxx -p 22
```
**注意事项**：

* 此处的username是cent os 中的账户名；

* xxx.xxx.xxx.xxx 是刚刚设置的静态ip；

* 22 表示连接的默认端口号，后期也可以进行编辑更改。

####  生成密钥对 (本机操作)

##### 进入当前用户目录，一般root用户只需要这样即可

```
cd ~
```
#####  创建.ssh目录并设置权限

```
mkdir -p .ssh
chmod 700 .ssh
```
##### 进入 .ssh 目录和生成密钥对

```
cd .ssh
ssh-keygen
```
然后，一路回车即可。

##### 注意事项：

* 一是 ssh-keygen 的详细用法，参见[ssh-keygen 中文手册](http://www.jinbuguo.com/openssh/ssh-keygen.html)

* 二是前面生成密钥的会显示地址，记住这个地址，需要把id_rsa.pub 的内容复制出来，后面会用到。

#### 生成密钥认证文件 （在服务器端或者通过ssh root操作）

##### 创建认证文件

```
touch authorized_keys
```

##### 编辑认证文件

```
vi /root/.ssh/authorized_keys
```

进入编辑界面后按i 开始编辑；把刚才复制的id_rsa.pub  内容复制到里面，稳妥起见，前面可以加一行注释，注释已#开头；

##### 保存并退出

按esc退出编辑状态，再按：wq 保存退出

##### 授权认证文件

```
chmod 600 authorized_keys
```

#### 修改SSH配置（在服务器端或者 ssh root操作）

##### 使用root登录修改配置文件：/etc/ssh/sshd_config

```
vi /etc/ssh/sshd_config
```
##### 将下面的内容的前面的 # 去掉

```
#PubkeyAuthentication yes
#AuthorizedKeysFile .ssh/authorized_keys

然后，修改下面的内容的 yes 为 no

PasswordAuthentication yes
```
修改后效果为

```
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no

```
##### 重启SSH服务

CentOS 6.x：

```
service sshd restart

```
CentOS 7.x：

```
systemctl restart sshd
```

#### 检验是否成功
 重启虚拟机，然后使用如下命令登陆
 
```
 ssh root@xxx.xxx.xxx.xxx
``` 

### Tips 

* Tab 键可以实现自动补全功能，输入命令、文件名及目录前几个字母后按 Tab，系统会自动补全，很有用。

* man 命令可以查询帮助文档。查看文档是按上下方向键或空格键翻页；按q 退出。







