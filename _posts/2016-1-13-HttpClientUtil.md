---
layout: post
title:  "如何利用HttpClient进行RestFul API接口对接"
date:   2015-12-12 14:06:05
categories: 企业框架
excerpt: 利用HttpClient与RestFul API对接
---

* content
{:toc}

## 浅谈RestFul API设计理念 
    （1）每一个URI代表一种资源；<br>
　　（2）客户端和服务器之间，传递这种资源的某种表现层；<br>
　　（3）客户端通过四个HTTP动词(POST/DELETE/PUT/GET)，对服务器端资源进行操作，实现"表现层状态转化"。。<br/>

## 为什么要用RestFul设计理念
  &nbsp;&nbsp;Logback是由log4j创始人设计的又一个开源日志组件。logback当前分成三个模块：logback-core,logback- classic和logback-access。
  logback-core是其它两个模块的基础模块。logback-classic是log4j的一个 改良版本。此外logback-classic完整实现SLF4J API使你可以很方便地更换成其它日志系统如log4j或JDK14 Logging。
  ogback-access访问模块与Servlet容器集成提供通过Http来访问日志的功能。[摘自百度百科](http://baike.baidu.com/link?url=gD7VVoX1Ld3nG66ol-ZcU7sm2g33mwQ9vvGQA7fLV4C92907ydESGM4S1Dg7p7o3KSCzvaytYu3bBL-YUlAv0_) <br/>

## 如何利用HttpClient与RestFul API对接
&nbsp;&nbsp;很多程序员都忽略了日志输出级别, 甚至不知道如何指定日志的输出级别. 相对于System.out来说, 日志框架有两个最大的优点就是可以指定输出类别(category)和级别(level). 对于日志输出级别来说, 下面是我们应该记住的一些原则: <br/>

### jar包引入:
&nbsp;&nbsp;系统发生了严重的错误, 必须马上进行处理, 否则系统将无法继续运行. 比如, NPE, 数据库不可用等. <br/>

### 封装请求:
&nbsp;&nbsp;系统能继续运行, 但是必须引起关注. 对于存在的问题一般可以分为两类: 一种系统存在明显的问题(比如, 数据不可用), 另一种就是系统存在潜在的问题, 需要引起注意或者给出一些建议(比如, 系统运行在安全模式或者访问当前系统的账号存在安全隐患). 总之就是系统仍然可用, 但是最好进行检查和调整. <br/>

### 调用请求:
&nbsp;&nbsp;重要的业务逻辑处理完成. 在理想情况下, INFO的日志信息要能让高级用户和系统管理员理解, 并从日志信息中能知道系统当前的运行状态. 比如对于一个机票预订系统来说, 当一个用户完成一个机票预订操作之后, 提醒应该给出"谁预订了从A到B的机票". 另一个需要输出INFO信息的地方就是一个系统操作引起系统的状态发生了重大变化(比如数据库更新, 过多的系统请求). <br/>

### 响应结果:
&nbsp;&nbsp;主要给开发人员看. <br/>
有不同观点的或者我的理解不对的地方请联系我，咱们一起讨论进步。
