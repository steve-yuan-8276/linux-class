---
title: 'Linux运维学习笔记1-3：如何使用putty、xshell、iterm2进行ssh登陆'
date: 2018-03-14 12:12:01
categories: linux
tags: [centos, linux, putty, xshell, iterm2, notes] 
---

### 写在前面

因为笔者用的是mac，在虚拟机中windows和centos双开，再在win7中SSH登陆centos。所以重点是先做mac下的iterm2连接；putty和xshell简略说明。

### 备份虚拟机的方法：

**备份很重要！备份很重要！备份很重要！（重要的事情说三遍）**

点击 ` virtual machine `，接着点击 `snapshots` 

![](https://farm1.staticflickr.com/784/26058108587_2887f191fc_o.png)

<!--more-->

如图，点击` curent state` ，之后在弹出的对话框里如数名称和注释，标识一下目前进展到什么程度了，最后点击OK就完成备份了。

![](https://farm5.staticflickr.com/4795/27059699798_6f11bbed6a_o.png)

想要回复到之前到备份，只要选择 `restore snashots` 即可。

![](https://farm1.staticflickr.com/813/40931404571_a6ac685450_o.png)

**注意：**备份的时机很重要，这一点跟git当中的commit有点像。建议完成一个阶段备份一次，不要太密集，浪费磁盘空间；也不要间隔时间太长，否则一旦回复，可能辛苦半天配置正确的操作也被覆盖掉了。

### iterm 2 连接

#### iterm2 下载安装

* 方法一： [官网](https://www.iterm2.com/index.html)  下载安装包，解压后拖到application文件夹即可。

* 方法二：brew安装

```
// 先安装 brew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

// 安装brew cask
brew install caskroom/cask/brew-cask

// 安装iterm2
brew cask install iterm2
```

#### 基本设置

```
// 设置显示字体
iTerm2 -> Preferences -> Text -> Change Font -> Source Code Pro 15pt

// 设置窗口大小
iTerm2 -> Preferences -> Window -> Setting for New Windows -> Columns -> 100 -> Rows -> 30
```

iterm 2的更多使用技巧可以参见[这个教程](http://wulfric.me/2015/08/iterm2/)

#### ip连接虚拟机

```
// 终端输入
ssh username@xxx.xxx.xxx.xxx -p 22
```
![](https://lh3.googleusercontent.com/-051WtkX0xsE/WqjBsqnCRcI/AAAAAAABhio/soMdwI9cFFErcfmcBDxTBtPlq57Gg3z2QCHMYCw/I/15210090665231.jpg)

**注意事项**：

* 此处的username是cent os 中的账户名；

* xxx.xxx.xxx.xxx 是虚拟机刚刚设置的静态ip；

* 22 表示连接的默认端口号

### 秘钥认证连接虚拟机

**总体思路：**

- 假设有两台电脑A和B，这里的A是你要操作的电脑；B是虚拟机。

- 先在A电脑生成密匙对（公钥、私钥各一个），私钥始终保存在A电脑不动，公钥复制出来，添加到电脑B的`authorized_ keys` 里面，再进行一些必要的设置，就可以从A电脑面输入密码登陆B电脑了。

- **注意：**如果已经在A电脑上设置过keygen，比如笔者已经在搞github折腾过一回，密匙对已经存在，就不用重新生成了，只要公钥复制出来粘贴过去就可以了。

####  生成密钥对 (在A电脑操作)

##### 进入当前用户目录，一般root用户只需要这样即可

```
cd 
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

* ssh-keygen 的详细用法，参见[ssh-keygen 中文手册](http://www.jinbuguo.com/openssh/ssh-keygen.html)

* 前面生成密钥的会显示地址，记住这个地址，需要使用 `cat <filename>` 查看，把id_rsa.pub 的内容复制出来，后面会用到。

#### 编辑密钥认证文件 （在B电脑操作或者通过ssh 连接B电脑操作）

##### 创建认证文件

```
touch authorized_keys
```

##### 编辑认证文件

```
vi /root/.ssh/authorized_keys
```

进入编辑界面后按i 开始编辑；把刚才复制的`id_rsa.pub ` 内容复制到里面，稳妥起见，前面可以加一行注释，注释以#开头；

##### 保存并退出

按esc退出编辑状态，再按 ` :wq` 保存退出

##### 授权认证文件

```
chmod 600 authorized_keys
```

#### 修改SSH配置（在B电脑操作或者通过ssh 连接B操作）

##### 使用root登录修改配置文件：/etc/ssh/sshd_config

```
vi /etc/ssh/sshd_config
```
##### 将下面的内容的前面的 # 去掉

```
# PubkeyAuthentication yes
# AuthorizedKeysFile .ssh/authorized_keys

//  然后，修改下面的内容的 yes 为 no

PasswordAuthentication yes
```
修改后效果为

```
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no
```
#### 关闭SElinux

##### 临时关闭

```
setenforce 0
```

##### 永久关闭

输入`vi /etc/selinux/config`，编辑这个文件，找到`SELINUX=enforcing `这一行，将`enforcing `改为`disabled`，保存退出。

#### 重启SSH服务

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

### putty 基本使用

#### 下载安装：

[putty官网下载地址](https://www.chiark.greenend.org.uk/~sgtatham/putty/) 最新版本是0.70；安装一路默认下一步即可。

#### putty基本使用：

##### ip连接

填写ip地址，`sessions `里面填写计算机名（随便取一个），端口`22`，连接方式`ssh`都是默认的，不用改，填好后点击`open`即可。

![](https://farm5.staticflickr.com/4785/26058599937_c63e0e5a4f_o.png)

第一次连接会询问是否信任，当然选`yes`了，理论上没错的话就可以登陆了。但是且慢，因为前面已经禁止密码登陆了，所以还要配置一下秘钥认证登陆。

##### 使用秘钥认证连接

###### 生成秘钥对

开始菜单打开puttygen，点击`generate `生成秘钥对，图上标记的地方是公钥的内容，这个很关键，最下面一行有`save public key`（保存公钥）和 `save private key`（保存私钥），分别点击保存下来。

![](https://farm5.staticflickr.com/4780/40890017832_0689d5176c_o.png)

###### 修改centOS配置文件

标准答案是这样的：

```
mkdir /root/.ssh    //新建 .ssh
chmod 700 /root/.ssh //更改目录权限
vi /root/.ssh/authorized_keys  //把公钥内内容复制粘贴到里面。
```

但是因为我们已经做过一遍了，所以只要把公钥入 /root/.ssh/authorized_keys 保存退出即可。就像这样：

![](https://farm1.staticflickr.com/804/40890237392_641ef03b77_o.png)

**注意：** 为了防止混淆可以 增加一行注释，注释以 `#` 开头，完成修改后按 `esc `，输入` :wq `退出编辑。

###### 配置putty

重新打开putty，选择刚才设置的`sessions`，点击`load`，点击 ` ssh` ，如图所示：
![](https://farm1.staticflickr.com/792/40890951162_cf4fbcb7fa_o.png)

点击 auth ，点击browser， 选择之前保存的 private key 文件，添加进去。

![](https://farm5.staticflickr.com/4776/40933064381_b0ebb0b0d0_o.png)

返回`sessions`页面，点击 `save `，之后再点击 `open `，就可以登陆虚拟机了。

另，putty是可以支持中文输入的，设置方法：
`Window`->`Appearence`->`Font:change`->黑体，字符集选择`CHINESE_GB2312`。

![](https://farm1.staticflickr.com/815/40933300141_3bebf04fe0_o.png)

### xshell 连接

xsehll的使用跟putty大同小异，下载地址在[xshell官网](https://www.netsarang.com/products/xsh_key_features.html) 最新版本是 xshell 5。但是考虑到xshell是付费的软件，所以就不折腾了。


### 参考资料

* [putty和xsehll特点比较](https://www.netsarang.com/products/xsh_key_features.html)

* [手把手教给你使用putty和xshell远程连接linux](http://blog.51cto.com/13518197/2050271)

* [putty使用教程(总结)](https://www.cnblogs.com/yuwentao/archive/2013/01/06/2846953.html)

* [Putty完全使用方法](http://www.putty.ws/Putty-wanquanshiyong)

* [Putty使用教程（linux）](http://www.pubyun.com/help/index.php?title=Putty%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B%EF%BC%88linux%EF%BC%89)

