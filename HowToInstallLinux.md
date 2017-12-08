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
systemctl restart network.service

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

### 配置远程ssh 登陆
#### step 1:  生成密钥对 (本机操作)

##### 1. 进入当前用户目录，一般root用户只需要这样即可

```
cd ~
```
#####  2. 创建.ssh目录并设置权限

```
mkdir -p .ssh
chmod 700 .ssh
```
##### 3. 进入 .ssh 目录和生成密钥对

```
cd .ssh
ssh-keygen
```
然后，一路回车即可

这里要注意的是：

* 一是 ssh-keygen 的详细用法，参见[ssh-keygen 中文手册](http://www.jinbuguo.com/openssh/ssh-keygen.html)

* 二是前面生成密钥的会显示地址，记住这个地址，需要把id_rsa.pub 的内容复制出来，后面会用到。

#### step 2: 生成密钥认证文件 （在服务器端或者通过ssh root操作）

```
1. 创建认证文件
touch authorized_keys

2. 编辑认证文件
vi /root/.ssh/authorized_keys
进入编辑界面后按i 开始编辑；
把刚才复制的id_rsa.pub  内容复制到里面，稳妥起见，前面可以加一行注释，注释已#开头；
最后按esc退出编辑状态，再按：wq 保存退出
3. 授权认证文件
chmod 600 authorized_keys
```

做完这三步，授权文件就搞定了。

#### step 3: 修改SSH配置（在服务器端或者 ssh root操作）

##### 1. 使用root登录修改配置文件：/etc/ssh/sshd_config

```
vi /etc/ssh/sshd_config
```
##### 2. 将下面的内容的前面的 # 去掉

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
##### 3. 最后重启SSH服务

CentOS 6.x：

```
service sshd restart

```
CentOS 7.x：

```
systemctl restart sshd
```

step 4: 检验是否成功
 重启虚拟机，然后使用如下命令登陆
 
```
 ssh root@xxx.xxx.xxx.xxx

``` 

