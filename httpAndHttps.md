# 网络基础：从HTTP 到 HTTPS

### 写在前面

最近一个月都在忙着投简历换工作，要说其中最大的意外，就是在全然不抱希望的前提下给阿里巴巴投递前端工程师的简历，竟然还接到了面试电话。
惊不惊喜？意不意外？
反正我第一时间以为是淘宝客服来搞推销的，问清楚是面试电话后完全懵逼。从第一个问题开始就不知道自己再说什么……毫无例外滴挂掉了。
好吧，这也是真实实力的体现，革命尚未成功，同志仍需努力，加油吧！

哦，对了，这里说的第一个问题，就是http和https的问题。

### 切入正题

#### HTTP:超文本传输协议（HyperText Transfer Protocol，缩写：HTTP）

HTTP协议，是互联网上应用最广泛的一种网络传输协议，默认使用80端口，主要用来在计算机网络之间进行通信。

##### HTTP工作原理：

![](https://lh3.googleusercontent.com/-cD5sV2BnGmc/WmB4UqzGHMI/AAAAAAABgEs/inzQcwbiwtwGV48xCmtfWGDYK0Kbg-pOQCHMYCw/I/15162716952948.jpg)

##### HTTP请求/响应的步骤：

1. 客户端连接到Web服务器，默认端口为80，建立一个TCP套接字连接。
2. 通过TCP套接字，发送HTTP请求。
3. 服务器接受请求并返回HTTP响应。
4. Web服务器主动关闭TCP套接字，释放TCP连接；客户端被动关闭TCP套接字，释放TCP连接。
5. 客户端浏览器解析HTML内容。

##### HTTP协议的缺点：

由于HTTP协议采用明文传输，所以天生有两大弊端：

1. 隐私泄露：不解释
2. 安全隐患：页面劫持、中间人攻击

为了克服上述弊端，HTTPS应运而生。

#### HTTPS：HTTPS：超文本传输安全协议（Hypertext Transfer Protocol Secure，缩写：HTTPS）

跟HTTP相比，最大的特点就写在脸上，secure，安全。那么，他是如何做到的呢？

##### HTTPS工作原理：

![](https://lh3.googleusercontent.com/-lH6C_xUN3To/WmB6ZyZeMfI/AAAAAAABgE4/R7AdtYXSCIUY_Sks_XiXicvv8fh585fBwCHMYCw/I/15162722299193.jpg)


1. 客户使用HTTPS的URL访问Web服务器，要求与Web服务器建立SSL连接。　　
2. Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。　　
3. 客户端的浏览器与Web服务器开始协商SSL/TLS连接的安全等级，也就是信息加密的等级。　　
4. 客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。　　
5. Web服务器利用自己的私钥解密出会话密钥。　
6. Web服务器利用会话密钥加密与客户端之间的通信。

整个这个过程，主要涉及CA证书和公钥方面的知识。
这样做的优点在于：

* 数据安全
* 保护隐私
* 基于上述原因，在搜索收录的时候，SEO的排名也会更高一丢丢。

当然，缺点也是有的，主要是：

* 网址域名绑定配置比较麻烦；
* 加载时间延长，消耗系统资源；
* SSL证书信用链体系并不安全，特别是在某些国家可以控制CA根证书的情况下，中间人攻击一样可行；

##### HTTPS证书的配置：

 此处省略五百字，回头补上。我的blog使用的是CloudFlare提供的UniversalSSL，重点是大写的免费！
 具体的备注过程之前整理过，地址在[这里](https://www.steve-yuan.com/2017/12/15/setHttps/)
 
 

