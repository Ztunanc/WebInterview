## https
- [https](#https)
  - [1.https的访问过程](#1https的访问过程)
    - [详细解释：](#详细解释)
  - [2.https的优缺点？](#2https的优缺点)
    - [1. 优点](#1-优点)
    - [2、缺点](#2缺点)
  - [3.https如何进行性能优化？](#3https如何进行性能优化)
    - [1.https访问速度优化](#1https访问速度优化)
    - [2.https计算性能优化](#2https计算性能优化)
  - [4.http和https](#4http和https)
  - [5.HTTPS为什么是安全的？](#5https为什么是安全的)
  - [6.HTTPS相对于HTTP的缺陷？](#6https相对于http的缺陷)
  - [7.为什么需要证书?](#7为什么需要证书)
  - [8.怎么防止的篡改?](#8怎么防止的篡改)
  - [参考链接](#参考链接)

### 1.https的访问过程

1.客户使用https的URL访问Web服务器，要求与Web服务器建立SSL连接。

2.Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。

3.客户端的浏览器与Web服务器开始协商SSL连接的安全等级，也就是信息加密的等级。

4.客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。

5.Web服务器利用自己的私钥解密出会话密钥。

6.Web服务器利用会话密钥加密与客户端之间的通信。

![](./images/https1.png)

#### 详细解释：

1. 客户端发起HTTPS请求

用户在浏览器里输入一个https网址，然后连接到**server的443（http的为80）**端口。

2.服务端的配置

就是指上述提到的数字证书；

3.传送证书

Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。

4.客户端解析证书

客户端会对证书进行判断，验证公钥是否有效，存在问题弹出会警告；若没有问题，生成一个随机值（私钥），然后用证书继续进行加密；

5.传送加密信息

客户端将上加密后的随机值（私钥）提供给服务端，服务端会对其进行解密；

6.服务端解密信息

服务端解密后得到随机值（私钥），然后**把内容通过该值进行对称加密**。对称加密就是指把要返回的信息和随机值（私钥）混合加密，这样除非知道随机值（私钥），不然无法获取数据。
   - 对称加密和非对称加密：
     - 对称加密：加密和解密用的是同一个密码或者同一套逻辑的加密方式。如指纹解锁、PIN解锁、账号密码等
     - 非对称加密用的是一对秘钥，分别叫做公钥（public key）和私钥（private key）。既可以加密也可以认证。
     - 非对称加密和对称加密对比
       - 对称加密是一个秘钥，非对称加密是一对，两个秘钥
       - 非对称加密比起对称加密更安全，因为不存在秘钥泄露问题，公钥即便被知道也没关系
       - 由于使用非对称加密在计算上特别复杂，所以一般来说对称加密的加密解密的速度相对于非对称加密快很多
       - 非对称秘钥还可以用于认证
       - 由于以上第三条，所以在 https 中传输数据时不会使用非对称加密加密传输数据，传输数据时有可能数据本身很大，那样的话非对称加密更耗时了，所以传输数据时不会使用非对称加密的方式加密。

7.传输加密后的信息

继续将加密后的信息传递给客户端；

8.客户端解密信息

客户端用之前生成的私钥（随机值）解密服务端传过来的信息，于是获取了解密后的内容。

### 2.https的优缺点？

#### 1. 优点

1.正确发送数据到客户端

使用HTTPS协议可认证用户和服务器，确保数据发送到正确的客户机和服务器

2.更安全

HTTPS协议是由**SSL+HTTP协议**构建的可进行加密传输、身份认证的网络协议，要比http协议安全，可防止数据在传输过程中不被窃取、改变，确保数据的完整性

3.增加中间人攻击的成本

HTTPS是现行架构下最安全的解决方案，虽然不是绝对安全，但它大幅增加了中间人攻击的成本。

4.搜索排名更高

谷歌在2014跳转搜索算法，采用HTTPS加密的网站在搜索结果中的排名将会更高
百度也在2018年发布百度对HTTPS站点的扶持态度，表明HTTPS将作为优质特征之一影响搜索排序。

#### 2、缺点

1.页面渲染更耗时间

因为SSL的缘故，HTTPS协议握手阶段比较费时，会使页面的加载时间延长近50%；

2.成本增加

SSL证书需要花钱，功能越强大的证书费用越高；

3.HTTPS连接缓存不如HTTP高效

HTTPS连接缓存不如HTTP高效，会增加数据开销和功耗，甚至已有的安全措施也会因此而受到影响；

4.SSL证书通常需要绑定IP

SSL证书通常需要绑定IP，不能在同一IP上绑定多个域名，IPv4资源不可能支撑这个消耗。

5.有局限性

HTTPS协议的加密范围也比较有限，在黑客攻击、拒绝服务攻击、服务器劫持等方面几乎起不到什么作用。最关键的，SSL证书的信用链体系并不安全，特别是在某些国家可以控制CA根证书的情况下，中间人攻击一样可行。

### 3.https如何进行性能优化？

#### 1.https访问速度优化

1.设置HSTS

服务端返回一个 HSTS 的 http header，浏览器获取到 HSTS 头部之后，在一段时间内，不管用户输入www.baidu.com还是http://www.baidu.com，都会默认将请求内部跳转成https://www.baidu.com。

2.Session resume

Session Resume 顾名思义就是复用 Session，实现简化握手。

3.Nginx设置Ocsp stapling

OSCP Stapling 工作原理简单来说就是浏览器发起 Client Hello 时会携带一个 certificate status request 的扩展，服务端看到这个扩展后将 OCSP 内容直接返回给浏览器，完成证书状态检查。由于浏览器不需要直接向 CA 站点查询证书状态，这个功能对访问速度的提升非常明显。

4.使用 SPDY 或者 HTTP2

SPDY 最大的特性就是多路复用，能将多个 HTTP 请求在同一个连接上一起发出去，不像目前的 HTTP 协议一样，只能串行地逐个发送请求。
HTTP2支持多路复用，有同样的效果。

```
1. SPDY 和 HTTP2 目前的实现默认使用 HTTPS 协议。
2. SPDY 和 HTTP2 都支持现有的 HTTP 语义和 API，对 WEB 应用几乎是透明的。
```

5.False start

简单概括 False Start 的原理就是在 client_key_exchange 发出时将应用层数据一起发出来，能够节省一个 RTT。

#### 2.https计算性能优化

1. 优先使用 ECC椭圆加密算术。
   ECC 椭圆加密算术相比普通的离散对数计算速度性能要强很多。

![](./images/https2.png)

2.使用最新版的 openssl。

一般来讲，新版的 OpenSSL 相比老版的计算速度和安全性都会有提升。

3.硬件加速方案。

- SSL 专用加速卡。
- GPUSSL 加速。

4.TLS 远程代理计算

### 4.http和https

HTTP：(HyperText Transfer Protocol)超文本传输协议

HTTPS：(Hypertext Transfer Protocol Secure)超文本传输安全协议

HTTP和HTTPS协议的主要区别如下：
- HTTPS协议需要CA证书，费用较高；HTTP协议不需要
- HTTP协议是超文本传输协议，信息是**明文传输**的，HTTPS则是具有安全性的SSL加密传输协议
- 使用不同的连接方式，端口也不同，HTTP协议端口是80，HTTPS的协议端口是443
- HTTP协议连接很简单，是**无状态**的；HTTPS协议是有SSL和HTTP协议构建的可进行加密传输、身份认证的网络协议，比HTTP更加安全

### 5.HTTPS为什么是安全的？

HTTPS相对于HTTP协议，加入了TLS/SSL，它的全称为安全传输层协议，是介于TCP和HTTP之间的一层安全协议。

TLS/SSL的功能实现主要依赖三类基本算法：散列函数hash、对称加密、非对称加密。这三类算法的作用如下：
- 基于散列函数验证信息的完整性
- **对称加密**算法采用协商的秘钥对**数据加密**
- **非对称加密**实现**身份认证和秘钥协商**

![在这里插入图片描述](./images/20200717110728280.png)

### 6.HTTPS相对于HTTP的缺陷？

- HTTPS需要做服务器和客户端双方的加密个解密处理，耗费更多服务器资源，**过程复杂**
- HTTPS协议**握手阶段比较费时**，增加页面的加载时间
- SSL证书是**收费**的，功能越强大的证书费用越高
- HTTPS连接服务器端**资源占用高**很多，支持访客稍多的网站需要投入**更大的成本**

### 7.为什么需要证书?

- 防止中间人攻击
- 验证服务器身份

### 8.怎么防止的篡改?

证书是公开的,虽然中间人可以拿到证书,但**私钥无法获取**,公钥无法推断出私钥,所以篡改后不能用私钥加密,强行加密客户也无法解密,强行修改内容,会导致证书内容与签名中的指纹不匹配

### 参考链接

https://blog.csdn.net/qq_42033567/article/details/107902340

https://www.cnblogs.com/Duikerdd/p/12030955.html
