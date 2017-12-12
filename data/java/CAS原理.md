# CAS原理与协议 
(转载自 http://blog.csdn.net/aboy123/article/details/35810867)
标签（空格分隔）： Java

---

**SSO英文全称Single Sign On，单点登录**。SSO是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。

**SSO的解决方案很多**，比如收费的有UTrust、惠普灵动等，开源的有CAS、Smart SSO等，**其中应用最为广泛的是CAS**。


**CAS （Central Authentication Service）中央认证服务**。CAS是一款不错的针对 Web 应用的单点登录框架。

CAS 具有以下特点：

 - 开源的企业级单点登录解决方案。
 - CAS Server 为需要独立部署的 Web 应用。
 - CAS Client 支持非常多的客户端(这里指单点登录系统中的各个 Web 应用)，包括 Java, .Net, PHP, Perl, Apache, uPortal, Ruby 等。


##CAS 原理和协议

结构上： CAS 包含两部分

###1、CAS Server

CAS Server 负责完成对用户的认证工作， CAS Server 需要独立部署，有不止一种 CAS Server 的实现。

CAS Server 会处理用户名 / 密码等凭证 (Credentials) ，它可能会到数据库检索一条用户帐号信息，也可能在 XML 文件中检索用户密码，对这种方式， CAS 均提供一种灵活但同一的接口 / 实现分离的方式， CAS 究竟是用何种认证方式，跟 CAS 协议是分离的，也就是，这个认证的实现细节可以自己定制和扩展。

###2、CAS Client

CAS Client 负责部署在客户端（指 Web 应用），原则上， CAS Client 的部署意味着，当有对本地 Web 应用的受保护资源的访问请求，并且需要对请求方进行身份认证， Web 应用不再接受任何的用户名密码等类似的 Credentials ，而是重定向到 CAS Server 进行认证。

目前， CAS Client 支持（某些在完善中）非常多的客户端，包括 Java 、 .Net 、 ISAPI 、 Php 、 Perl 、 uPortal 、 Acegi 、 Ruby 、 VBScript 等客户端，几乎可以这样说， CAS 协议能够适合任何语言编写的客户端应用。


协议：
整个协议的基础思想都是基于票据方式。下面，我们看看CAS的基本协议框架：
![此处输入图片的描述][1]

**CAS Client 与受保护的客户端应用部署在一起，以 Filter 方式保护受保护的资源。对于访问受保护资源的每个 Web 请求，CAS Client 会分析该请求的 Http 请求中是否包含 Service Ticket，如果没有，则说明当前用户尚未登录，于是将请求重定向到指定好的 CAS Server 登录地址，并传递 Service （也就是要访问的目的资源地址），以便登录成功过后转回该地址。用户在第 3 步中输入认证信息，如果登录成功，CAS Server 随机产生一个相当长度、唯一、不可伪造的 Service Ticket，并缓存以待将来验证，之后系统自动重定向到 Service 所在地址，并为客户端浏览器设置一个 Ticket Granted Cookie（TGC），CAS Client 在拿到 Service 和新产生的 Ticket 过后，在第 5，6 步中与 CAS Server 进行身份合适，以确保 Service Ticket 的合法性。**


在该协议中，所有与 CAS 的交互均采用 SSL 协议，确保，ST 和 TGC 的安全性。协议工作过程中会有 2 次重定向的过程，但是 CAS Client 与 CAS Server 之间进行 Ticket 验证的过程对于用户是透明的。


另外，CAS 协议中还提供了 Proxy （代理）模式，以适应更加高级、复杂的应用场景


 **认证流程分析：**

1. 用户第一次访问受保护的应用。受保护的应用通过其中配置的统一认证过滤器队请求进行过滤，未发现在session中有特定的用户信息，也未发现有ST参数。

2. 应用系统将认定用户第一次进入受保护的系统中，重定向到统一认证系统中特定的路径。通常该路径为 http:// 统一认证IP：端口/casserver/login

3. 统一认证系统判断用户在统一认证系统中是否登录过。

4. 如果没有登录过，则将用户定向到登录界面。

5. 用户在登录界面输入用户名和密码等信息，并进行提交。

6. 统一认证系统验证用户提交的凭证是否正确，如果正确，生成cookie形式的TGT（ticket grant ticket）和一个ST（service ticket）。并通过重定向跳回到受保护的系统中。并且，将ST作为参数附加在URL后面。

7. 进入受保护系统中，请求经由统一认证过滤器进行过滤，发现虽然在session中不存在特定的用户信息，但是存在ST票据。

8. 有统一认证客户端持有ST票据通过http请求，发送到统一认证端进行认证票据的有效性。

9. 统一认证系统认证票据有效，相应用户信息到受保护系统。

10. 受保护系统获得用户信息，在session中设置特定的用户信息。返回用户访问资源。


  [1]: http://img.blog.csdn.net/20140629194018109?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWJveTEyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center
