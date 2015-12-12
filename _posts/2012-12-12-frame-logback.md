---
layout: post
title:  "在spring boot里面进行logback的搭建"
date:   2015-12-12 14:06:05
categories: 企业框架
excerpt: logbak搭建
---

* content
{:toc}

## 前言 
  &nbsp;&nbsp;今天在联调的时候看到密密麻麻的日志信息，我就在想企业里面用的logback是怎么搭建，怎么进行日志管理，和我以前用的log4j有啥区别呢？
 带着好奇的心态，来探究探究logback有啥让我们惊奇的地方。<br/>

## logback的前世今生
  &nbsp;&nbsp;Logback是由log4j创始人设计的又一个开源日志组件。logback当前分成三个模块：logback-core,logback- classic和logback-access。
  logback-core是其它两个模块的基础模块。logback-classic是log4j的一个 改良版本。此外logback-classic完整实现SLF4J API使你可以很方便地更换成其它日志系统如log4j或JDK14 Logging。
  ogback-access访问模块与Servlet容器集成提供通过Http来访问日志的功能。[摘自百度百科](http://baike.baidu.com/link?url=gD7VVoX1Ld3nG66ol-ZcU7sm2g33mwQ9vvGQA7fLV4C92907ydESGM4S1Dg7p7o3KSCzvaytYu3bBL-YUlAv0_) <br/>

## logback的四种级别你知道吗？
&nbsp;&nbsp;很多程序员都忽略了日志输出级别, 甚至不知道如何指定日志的输出级别. 相对于System.out来说, 日志框架有两个最大的优点就是可以指定输出类别(category)和级别(level). 对于日志输出级别来说, 下面是我们应该记住的一些原则: <br/>

### ERROR:
&nbsp;&nbsp;系统发生了严重的错误, 必须马上进行处理, 否则系统将无法继续运行. 比如, NPE, 数据库不可用等. <br/>

### WARN:
&nbsp;&nbsp;系统能继续运行, 但是必须引起关注. 对于存在的问题一般可以分为两类: 一种系统存在明显的问题(比如, 数据不可用), 另一种就是系统存在潜在的问题, 需要引起注意或者给出一些建议(比如, 系统运行在安全模式或者访问当前系统的账号存在安全隐患). 总之就是系统仍然可用, 但是最好进行检查和调整. <br/>

### INFO:
&nbsp;&nbsp;重要的业务逻辑处理完成. 在理想情况下, INFO的日志信息要能让高级用户和系统管理员理解, 并从日志信息中能知道系统当前的运行状态. 比如对于一个机票预订系统来说, 当一个用户完成一个机票预订操作之后, 提醒应该给出"谁预订了从A到B的机票". 另一个需要输出INFO信息的地方就是一个系统操作引起系统的状态发生了重大变化(比如数据库更新, 过多的系统请求). <br/>

### DEBUG:
&nbsp;&nbsp;主要给开发人员看. <br/>

### TRACE: 
&nbsp;&nbsp;系统详细信息, 主要给开发人员用, 一般来说, 如果是线上系统的话, 可以认为是临时输出, 而且随时可以通过开关将其关闭. 有时候我们很难将DEBUG和TRACE区分开, 一般情况下, 如果是一个已经开发测试完成的系统, 再往系统中添加日志输出, 那么应该设为TRACE级别.<br/> 
以上只是建议, 你也可以建立一套属于你自己的规则. 但是一套良好的日志系统, 应该首先是能根据情况快速灵活的调整日志内容的输出. 

## 和以往的log4j比较
&nbsp;&nbsp;老实说我之前用了log4j,感觉很不错，为什么企业都愿意使用logback呢？用过之后发现logback给我们带来的方面有下面几点就能看出来：<br/>

### 更快的实现 
&nbsp;&nbsp;我尝试着测试相同一个类的一个方法执行10000遍，使用logback的日志打印速度要远远比log4j打印的速度快10倍左右，百度一下说Logback的内核重写了，在一些关键执行路径上性能提升数十倍以上。而且logback不仅性能提升了，初始化内存加载也更小了。 <br/>

### 非常充分的测试 
&nbsp;&nbsp;Logback经过了几年，数不清小时的测试。Logback的测试完全不同级别的。在作者的观点，这是简单重要的原因选择logback而不是log4j。<br/> 

### Logback-classic非常自然实现了SLF4j 
&nbsp;&nbsp;Logback- classic实现了SLF4j。在使用SLF4j中，你都感觉不到logback-classic。而且因为logback-classic非常自然地实现了SLF4J，所以切换到log4j或者其他，非常容易，只需要提供成另一个jar包就OK，根本不需要去动那些通过SLF4JAPI实现的代码。 <br/>

### 非常充分的文档 
&nbsp;&nbsp;官方网站有两百多页的文档。<br/> 

### 自动重新加载配置文件 
&nbsp;&nbsp;当配置文件修改了，Logback-classic能自动重新加载配置文件。扫描过程快且安全，它并不需要另外创建一个扫描线程。这个技术充分保证了应用程序能跑得很欢在JEE环境里面。 <br/>

## logback如何配置--maven项目中搭建

### pom.xml配置，映入相应的jar文件
&nbsp;&nbsp;在maven项目中加入以下配置，maven自动从远程仓库中下载相应版本的jar包，不是maven项目的同学只要在lib目录下放置一下几个jar包即可，我就不多说，您懂得！<br/>
<pre><code class="markdown">
></properties>
>        <slf4j-version>1.7.5</slf4j-version>
>		<logback-core-version>1.0.13</logback-core-version>
>		<logback-classic-version>1.0.13</logback-classic-version>
></properties>
<!-- logback start -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${slf4j-version}</version>
			<type>jar</type>
			<scope>compile</scope>
		</dependency>

		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
			<version>${logback-core-version}</version>
			<type>jar</type>
		</dependency>

		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>${logback-classic-version}</version>
			<type>jar</type>
		</dependency>
		<!-- logback end -->
</code></pre><br/>

### 在logback.xml配置文件中设置日志格式
&nbsp;&nbsp;在上步完成之后就要对我们的日志进行格式配置了，一个好的日志格式对以后海量日志查询出bug有很大的帮助。其中我们要将logback.xml放置在类路径下，便于管理<br/>
<pre><code class="markdown">
<configuration>
	<!-- 设置日志在控制台上打印的格式 分别对应日志生成时间+对应的站点执行方法+日志级别+日志出现的类+日志消息 -->
	<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>%d [%t] %-5p [%c] - %m%n</pattern>
		</encoder>
	</appender>
	<!-- 演示按时间滚动的策略 -->
	<appender name="linhao007"
		class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>${catalina.base}/logs/linhao007.log</file>
		<!-- 设置日志滚动按照时间的日志命名  -->
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<!-- 设置产生日志存在的地方 -->
			<fileNamePattern>/tmp/logs/linhao007.%d{yyyy-MM-dd}.log
			</fileNamePattern>
			<!-- 保留 30天数据，默认无限 -->
			<maxHistory>30</maxHistory>
		</rollingPolicy>
		<encoder>
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS}####%m%n</pattern>
		</encoder>
	</appender>
	<!--myibatis log configure 并將这些日志交于root去管理，本身并不打印-->
	<logger name="com.apache.ibatis" level="TRACE" additivity="false">
		<appender-ref ref="sql" />
	</logger>
	<logger name="java.sql.Connection" level="DEBUG" additivity="false">
		<appender-ref ref="sql" />
	</logger>
	<logger name="java.sql.Statement" level="DEBUG" additivity="false">
		<appender-ref ref="sql" />
	</logger>
	<logger name="java.sql.PreparedStatement" level="DEBUG"
		additivity="false">
		<appender-ref ref="sql" />
	</logger>
	<!-- 这个配置是为了管理整个自定义日志系统，root将日志的级别为“INFO”及大于“INFO”级别的日志信息交给已经配置好的名称为“console” 
		的appender处理，“console”appender将信息打印到控制台 ,name属性表示在这个包下所有信息都会被日志管理 ，并在linhao007指定文件中生成日志文件-->
	<root name="com.daojia_58.linhao" level="info">
		<appender-ref ref="console" />
		<appender-ref ref="linhao007" />
	</root>
</configuration>
</code></pre><br/>

### 在web.xml中进行日志监听配置
&nbsp;&nbsp;你以为这样就万事大吉了么？NO!NO！NO!too young too simple!我们仅仅只是搭建了logback,但是还没有交给容器管理监听，所以我们还需要给web.xml配置监听：<br/>
<pre><code class="markdown">
<context-param>
		<param-name>logbackConfigLocation</param-name>
		<param-value>
			classpath*:/logback.xml
		</param-value>
	</context-param>
	<listener>
		<listener-class>ch.qos.logback.ext.spring.web.LogbackConfigListener</listener-class>
	</listener>
</code></pre><br/>

## 结果分析 
&nbsp;&nbsp;进行上面的配置之后整个logback就搭建好了，接下来是如何使用日志了，首先我们要在日志监听的类上定义日志操作对象：<br/>
<pre><code class="markdown">
private static Logger logger =  LoggerFactory.getLogger(TestLog.class);
</code></pre><br/>
在创建这个对象的时候必须传入日志监听类如上面的：TestLog.clss;之后再相应的地方打下日志，我在这里列举出5中日志类型：<br/>
<pre><code class="markdown">
        logger.info("hello world");
	logger.error("出错管理"+e);
	logger.trace("hello world");
	logger.warn("hello world");
	logger.debug("hello world");
</code></pre><br/>

结果打印出：<br/>
<pre><code class="markdown">
2015-12-12 17:32:20,555 [main] INFO  [com.daojia_58.linhao.web.UserController] - hello world
2015-12-12 17:32:20,566 [main] ERROR [com.daojia_58.linhao.web.UserController] - 出错管理
2015-12-12 17:32:20,567 [main] WARN  [com.daojia_58.linhao.web.UserController] - hello world
</code></pre><br/>
有不同观点的或者我的理解不对的地方请联系我，咱们一起讨论进步。
