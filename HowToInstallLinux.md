# HowToInstallLinux

### Installation：
* 安装VMmare fusion
* 下载 cent OS

tips： 安装都是傻瓜式的，不过记得设置root 密码（不要轻易告诉别人）

### 设置静态IP
#### step 1: 查看当前网络状况，使用命令

```
#ip add

#ifconfig
```
如何不存在ip信息，可通过以下命令自动获取ip：

```
dhchlient
```
需要注意的是，这里需要记下来ip address、gateway、netmask、ensXX（网卡信息），后面设置静态ip会用到。

#### step 2 ：编辑网卡配置

```
#vi /etc/sysconfig/network-script-ensXX
```
注：此处的XX，就是前面查到的网卡信息。

```
需要更改的信息如下：
ONBOOT=yes（原来是no）；
BOOTPROTO=static（原来是dhclient）
下面是需要增加的信息：
IPADDR=xxx.xxx.xxx.xxx(dhclient获取的网址)
NETMASK=255.255.255.0（子网掩码）
GATEWAEY=xxx.xxx.xxx.xxx(网关地址)
DNS1=119.29.29.29（DNS服务器）
```

最后，先按“esc”退出编辑模式，之后再输入“:wq”进行保存。
#### step 3: 重启ip设置

```
sustemctl.network.service

```
正常情况下，静态ip就设置好。检查是否成功，可以ping一下外网，如：

```
ping www.baidu.com
```
### 远程连接此虚拟机

#### 前提条件：

* mac自带终端或者item

#### 输入如下命令进行连接：

```
ssh username@xxx.xxx.xxx.xxx -p 22
```
注意：
1.  此处的username是cent os 中的账户名
2.  xxx.xxx.xxx.xxx 是刚刚设置的静态ip
3.  22 表示连接的默认端口号，后期也可以进行编辑更改。






