# P5  The basis of Linux

### Required Software
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Vagrant](https://www.vagrantup.com/docs/index.html)

**TIPS: Read the documentation with question.**

### Up and Running

* $ vagrant init hashicorp/precise64
* $ vagrant up

### Course Environment

* Create a new folder on your computer where you’ll store your work for this course, then open that folder within your terminal.

* Type 'vagrant init ubuntu/trusty64' to tell Vagrant what kind of Linux virtual machine you would like to run.

* Type 'vagrant up' to download and start running the virtual machine

**Caution**

1. vagrant 安装的默认没有设置，第一次使用sudo命令时要求输入密码 vagrant 即可；
2. 输入sudo passwd 修改密码 

### become super user(待补充)

### passwd 识别规则

终端输入：

```
cat /etc/passwd
```

结果每行代表一个用户信息，如：

```
vagrant:x:1000:1000:vagrant,,,:/home/vagrant:/bin/bash
```
解析：
![BBC029AB-0ED0-43AB-BF81-105CFF97A9E7](https://lh3.googleusercontent.com/-HKDd4tg75u8/Wiar7cQiRqI/AAAAAAABfIo/ncn9BmRK7doL0xLZap4rweZ32VNMmrWQACHMYCw/I/BBC029AB-0ED0-43AB-BF81-105CFF97A9E7.png)

### 新建账户并实现ssh登陆

#### 新建账户

```
sudo adduser
```
注：一般设置全名，其他的可不添

### ssh登陆

1. 安装ssh

2. default SSH is listening on port 22, therefore use：

注：uadacity课程中提供的端口2222并非默认端口，直接登陆会出错。如果一定要用2222端口，需要进行如下修改：

##### step 1:

```
sudo nano /etc/ssh/sshd_config

```
##### step 2:

```
Port 22  to  Port 2222
```

##### step 3: reload the configuration

```
sudo service ssh force-reload
```

##### step 4:修改成功后，使用如下命令进行登陆：

```
ssh user@127.0.0.1 -p 2222

```



