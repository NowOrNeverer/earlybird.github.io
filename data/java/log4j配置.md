# log4j 配置详解

标签（空格分隔）： Java

---

Log4j 的配置文件用来设置记录器的级别、输出目的地和布局，可以是 log4j.properties 或 log4j.xml。以下讲解以 log4j.properties 的配置方式进行。

#配置文件的基本格式

基本格式如下：
```
    #配置根 Logger
    log4j.rootLogger = [ level ], appenderName1, appenderName2, ……
    #配置日志信息输出目的地 (Appender)
    log4j.appender.appenderName = fully.qualified.name.of.appender.class 
    log4j.appender.appenderName.option1 = value1 
    ……
    log4j.appender.appenderName.optionN = valueN 
    #配置日志信息的格式 (Layout)
    log4j.appender.appenderName.layout = fully.qualified.name.of.layout.class 
    log4j.appender.appenderName.layout.option1 = value1 
    ……
    log4j.appender.appenderName.layout.optionN = valueN
```
    
###Level
Level 是日志输出级别，共有5级：
| 名称 |  级别  |
|------|--------|
|FATAL |  0     |
|ERROR |  3     |
|WARN  |  4     |
|INFO  |  6     |
|DEBUG |  7     |

###Appender
Appender 为日志输出目的地，Log4j 提供的 appender 有以下几种：

- org.apache.log4j.ConsoleAppender（控制台）
- org.apache.log4j.FileAppender（文件）
- org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
- org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
- org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）

###ConsoleAppender 可配置项

| 项	| 解释 |
|------|------|
|Threshold=DEBUG|	指定日志消息的输出最低层次|
|ImmediateFlush=true|	默认值是 true，意味着所有的消息都会被立即输出|
|Target=System.err|	默认情况下是 System.out，指定输出控制台|

###FileAppender 可配置项

| 项	| 解释 |
|------|------|
|Threshold=DEBUF|	指定日志消息的输出最低层次
|ImmediateFlush=true|	默认值是 true，意味着所有的消息都会被立即输出
|File=mylog.txt|	指定消息输出到 mylog.txt 文件
|Append=false|	默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容

###RollingFileAppender 可配置项

| 项	| 解释 |
|------|------|
|Threshold=DEBUG|	指定日志消息的输出最低层次。|
|ImmediateFlush=true|	默认值是true,意味着所有的消息都会被立即输出。|
|File=mylog.txt	|指定消息输出到mylog.txt文件。|
|Append=false|	默认值是true即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。|
|MaxFileSize=100KB	|后缀可以是KB, MB 或者是 GB. 在日志文件到达该大小时，将会自动滚动，即将原来的内容移到mylog.log.1文件。|
|MaxBackupIndex=2|	指定可以产生的滚动文件的最大数。|

###Layout
Layout 为日志输出格式，Log4j 提供的 layout 有以下几种：

 - org.apache.log4j.HTMLLayout（以HTML表格形式布局），
 - org.apache.log4j.PatternLayout（可以灵活地指定布局模式），
 - org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），
 - org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）

###Layout 示例
```
    log4j.appender.A1.layout.ConversionPattern=%-4r %-5p %d{yyyy-MM-dd HH:mm:ssS} %c %m%n
```

	
| 符号	| 意义 |
|------|------|
|-	|信息输出时左对齐|
|%p	|输出日志信息优先级，即DEBUG，INFO，WARN，ERROR，FATAL|
|%d	|输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss,SSS}，输出类似：2002年10月18日 22:10:28,921|
|%r|	输出自应用启动到输出该log信息耗费的毫秒数|
|%c|	输出日志信息所属的类，通常就是所在类的全名|
|%t|	输出产生该日志事件的线程名|
|%l|	输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main (TestLog4.java:10)|
|%x|	输出和当前线程相关联的NDC(嵌套诊断环境),尤其用到像java servlets这样的多客户多线程的应用中。|
|%%|	输出一个"%"字符|
|%F|	输出日志消息产生时所在的文件名称|
|%L|	输出代码中的行号|
|%m	|输出代码中指定的消息,产生的日志具体信息|
|%n	|输出一个回车换行符，Windows平台为"\r\n"，Unix平台为"\n"|

###修饰符

可以在%与模式字符之间加上修饰符来控制其最小宽度、最大宽度和文本的对齐方式，如：
|示例	|表示|
|------|------|
|%20c|	指定输出category的名称，最小的宽度是20，如果category的名称小于20的话，默认的情况下右对齐。|
|%-20c|	指定输出category的名称，最小的宽度是20，如果category的名称小于20的话，"-"号指定左对齐。|
|%.30c|	指定输出category的名称，最大的宽度是30，如果category的名称大于30的话，就会将左边多出的字符截掉，但小于30的话也不会有空格。|
|%20.30c|	如果category的名称小于20就补空格，并且右对齐，如果其名称长于30字符，就从左边较远输出的字符截掉。|


