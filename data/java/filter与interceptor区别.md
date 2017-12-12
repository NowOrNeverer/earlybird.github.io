# servlet/filter/listener/interceptor区别与联系【转载】

标签（空格分隔）： Java

---
某天突然想起struts2和struts1的区别的时候，发现为什么struts1要用servlet，而struts2要用filter呢？一时又发现，servlet和filter有什么区别呢？于是看了看web.xml，一时又发现，咦，servlet、filter、listener？还有个interceptor？对于这几个概念，本应是初学者就掌握的东东了，可惜本人基础学的不好，只能是现在补课。于是就有了这篇博客。

慢慢来吧，需要补课的地方还有很多很多呀。初学的时候都不知道他们存在呢。呵呵。

下面从几个方面阐述一下题目中四个概念的区别与联系：

1、概念

2、生命周期

3、职责

4、执行过程

##一、概念：

1、servlet：servlet是一种运行服务器端的java应用程序，具有独立于平台和协议的特性，并且可以动态的生成web页面，它工作在客户端请求与服务器响应的中间层。

2、filter：filter是一个可以复用的代码片段，可以用来转换HTTP请求、响应和头信息。Filter不像Servlet，它不能产生一个请求或者响应，它只是修改对某一资源的请求，或者修改从某一的响应。

3、listener：监听器，从字面上可以看出listener主要用来监听只用。通过listener可以监听web服务器中某一个执行动作，并根据其要求作出相应的响应。通俗的语言说就是在application，session，request三个对象创建消亡或者往其中添加修改删除属性时自动执行代码的功能组件。

4、interceptor：是在面向切面编程的，就是在你的service或者一个方法，前调用一个方法，或者在方法后调用一个方法，比如动态代理就是拦截器的简单实现，在你调用方法前打印出字符串（或者做其它业务逻辑的操作），也可以在你调用方法后打印出字符串，甚至在你抛出异常的时候做业务逻辑的操作。

5、servlet、filter、listener是配置到web.xml中，interceptor不配置到web.xml中，struts的拦截器配置到struts.xml中。spring的拦截器配置到spring.xml中。

##二、生命周期：

1、servlet：servlet的生命周期始于它被装入web服务器的内存时，并在web服务器终止或重新装入servlet时结束。servlet一旦被装入web服务器，一般不会从web服务器内存中删除，直至web服务器关闭或重新结束。
(1)、装入：启动服务器时加载Servlet的实例；
(2)、初始化：web服务器启动时或web服务器接收到请求时，或者两者之间的某个时刻启动。初始化工作有init（）方法负责执行完成；
(3)、调用：从第一次到以后的多次访问，都是只调用doGet()或doPost()方法；
(4)、销毁：停止服务器时调用destroy()方法，销毁实例。

2、filter：（一定要实现javax.servlet包的Filter接口的三个方法init()、doFilter()、destroy()，空实现也行）
(1)、启动服务器时加载过滤器的实例，并调用init()方法来初始化实例；
(2)、每一次请求时都只调用方法doFilter()进行处理；
(3)、停止服务器时调用destroy()方法，销毁实例。

3、listener：类似于servlet和filter

web.xml 的加载顺序是：context- param -> listener -> filter -> servlet

4、interceptor：以struts的拦截器为例，加载了struts.xml以后，初始化相应拦截器。当action请求来时调用intercept方法，服务器停止销毁interceptor。

##三、职责

1、servlet：

创建并返回一个包含基于客户请求性质的动态内容的完整的html页面；
创建可嵌入到现有的html页面中的一部分html页面（html片段）；
读取客户端发来的隐藏数据；
读取客户端发来的显示数据；
与其他服务器资源（包括数据库和java的应用程序）进行通信；
通过状态代码和响应头向客户端发送隐藏数据。

2、filter：

filter能够在一个请求到达servlet之前预处理用户请求，也可以在离开servlet时处理http响应：
在执行servlet之前，首先执行filter程序，并为之做一些预处理工作；
根据程序需要修改请求和响应；
在servlet被调用之后截获servlet的执行

3、listener：职责如概念。

servlet2.4规范中提供了8个listener接口，可以将其分为三类，分别如下：
第一类：与servletContext有关的listner接口。包括：ServletContextListener、ServletContextAttributeListener
第二类：与HttpSession有关的Listner接口。包括：HttpSessionListner、HttpSessionAttributeListener、HttpSessionBindingListener、                      HttpSessionActivationListener；
第三类：与ServletRequest有关的Listener接口，包括：ServletRequestListner、ServletRequestAttributeListener

4、interceptor：与过滤器十分相似，通过层层拦截，处理用户的请求和响应。

 

备注：web.xml 的加载顺序是：context-param -> listener -> filter -> servlet 。了解了这几个概念的区别以后，不难理论这个加载顺序了。

 

##四、几个区别：

1,servlet 流程是短的，url传来之后，就对其进行处理，之后返回或转向到某一自己指定的页面。它主要用来在 业务处理之前进行控制.
2,filter 流程是线性的， url传来之后，检查之后，可保持原来的流程继续向下执行，被下一个filter, servlet接收等，而servlet 处理之后，不会继续向下传递。filter功能可用来保持流程继续按照原来的方式进行下去，或者主导流程，而servlet的功能主要用来主导流程。
filter可用来进行字符编码的过滤，检测用户是否登陆的过滤，禁止页面缓存等
3, servlet,filter都是针对url之类的，而listener是针对对象的操作的，如session的创建，session.setAttribute的发生，在这样的事件发生时做一些事情。
可用来进行：Spring整合Struts,为Struts的action注入属性，web应用定时任务的实现，在线人数的统计等

4，interceptor 拦截器，类似于filter,不过在struts.xml中配置，不是在web.xml,并且不是针对URL的，而是针对action,当页面提交action时，进行过滤操作，相当于struts1.x提供的plug-in机制，可以看作，前者是struts1.x自带的filter,而interceptor 是struts2 提供的filter.
与filter不同点：（１）不在web.xml中配置，而是在struts.xml中完成配置，与action在一起
( 2  ) 可由action自己指定用哪个interceptor 来在接收之前做事

5，struts2中的过滤器和拦截器的区别与联系：

（1）、拦截器是基于java反射机制的，而过滤器是基于函数回调的。
（2）、过滤器依赖与servlet容器，而拦截器不依赖与servlet容器。
（3）、拦截器只能对Action请求起作用，而过滤器则可以对几乎所有请求起作用。
（4）、拦截器可以访问Action上下文、值栈里的对象，而过滤器不能。
（5）、在Action的生命周期中，拦截器可以多次调用，而过滤器只能在容器初始化时被调用一次。
