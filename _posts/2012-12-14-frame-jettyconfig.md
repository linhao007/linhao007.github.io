---
layout: post
title:  "在spring boot里面进行jetty的启动"
date:   2015-12-14 14:06:05
categories: 企业框架
excerpt: 在IntelliJ IDEA的maven项目如何使用jetty启动
---

* content
{:toc}

## 前言 
  &nbsp;&nbsp;&nbsp;&nbsp;以前一直使用eclipse + tomcat来启动项目，今天在运行一个项目的时候被学长看到了，
  他说用tomcat启动很慢，叫我试一试jetty来启动web项目,所以特意了解了一下jetty.<br/>
  
## 浅谈jetty
&nbsp;&nbsp;&nbsp;&nbsp;Jetty 是一个开源的servlet容器，它为基于Java的web容器，例如JSP和servlet提供运行环境。Jetty是使用Java语言编写的，它的API以一组JAR包的形式发布。
开发人员可以将Jetty容器实例化成一个对象，可以迅速为一些独立运行（stand-alone）的Java应用提供网络和web连接。[摘自百度百科](http://baike.baidu.com/link?url=qISf-60N2e1v1DqK60Z5ZOLgZw_EM38M59mhbfzBAZqkIg1o0PPKsIsDD80Q4K14SeoRRBEpieEtWQTWdsXDpq) <br/>


## jetty和tomcat的区别
###相同点：
&nbsp;&nbsp;&nbsp;&nbsp;1.      Tomcat和Jetty都是一种Servlet引擎，他们都支持标准的servlet规范和JavaEE的规范。<br/>
 
 
### 不同点：
&nbsp;&nbsp;&nbsp;&nbsp;1.      架构比较<br/>
Jetty的架构比Tomcat的更为简单<br/>
Jetty的架构是基于Handler来实现的，主要的扩展功能都可以用Handler来实现，扩展简单。<br/>
Tomcat的架构是基于容器设计的，进行扩展是需要了解Tomcat的整体设计结构，不易扩展。<br/><br/>

&nbsp;&nbsp;&nbsp;&nbsp;2.      性能比较<br/>
Jetty和Tomcat性能方面差异不大<br/>
Jetty可以同时处理大量连接而且可以长时间保持连接，适合于web聊天应用等等。<br/>
Jetty的架构简单，因此作为服务器，Jetty可以按需加载组件，减少不需要的组件，减少了服务器内存开销，从而提高服务器性能。<br/>
Jetty默认采用NIO结束在处理I/O请求上更占优势，在处理静态资源时，性能较高<br/><br/>
 
Tomcat适合处理少数非常繁忙的链接，也就是说链接生命周期短的话，Tomcat的总体性能更高。<br/>
Tomcat默认采用BIO处理I/O请求，在处理静态资源时，性能较差。<br/>
 
&nbsp;&nbsp;&nbsp;&nbsp;3.      其它比较<br/>
Jetty的应用更加快速，修改简单，对新的Servlet规范的支持较好，所以比较受企业的欢迎。<br/>
Tomcat目前应用比较广泛，对JavaEE和Servlet的支持更加全面，很多特性会直接集成进来。<br/>

## IntelliJ IDEA的maven项目如何使用jetty启动
1、首先我们要在maven中安装jetty插件（其实就是下载一个jar包,用来与idea进行集成）这个插件只需要在pom.xml中进行如下配置：</br>
<pre><code class="markdown">
 <!--jetty插件-->
            <plugin>
                <groupId>org.mortbay.jetty</groupId>
                <artifactId>jetty-maven-plugin</artifactId>
                <version>${jetty.version}</version>
                <configuration>
                    <systemProperties>
                        <systemProperty>
                            <name>spring.profiles.active</name>
                            <value>production</value>
                        </systemProperty>
                    </systemProperties>
                    <useTestClasspath>true</useTestClasspath>
                    <war>${project.basedir}/target/platform-admin.war</war>
                    <webAppConfig>
                        <contextPath>/${project.artifactId}</contextPath>
                    </webAppConfig>
                    <connectors>                    
                        <!--可以自定义端口号，也可以在idea自己设置，下面会讲到如何在idea中设置端口号-->
                        <connector implementation="org.eclipse.jetty.server.nio.SelectChannelConnector">
                            <port>8086</port>
                        </connector>
                    </connectors>
                </configuration>
            </plugin>
</code></pre><br/>

2、之后配置jetty启动所依赖的jar包<br/>
<pre><code class="markdown">
 <!-- jetty -->
        <dependency>
            <groupId>org.eclipse.jetty.aggregate</groupId>
            <artifactId>jetty-webapp</artifactId>
            <version>${jetty.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jetty</groupId>
            <artifactId>jetty-jsp</artifactId>
            <version>${jetty.version}</version>
            <scope>test</scope>
        </dependency>
</code></pre><br/>

3、接下来在IDEA中进行MAVEN项目jetty启动配置：<br/>
1)首先在IDEA中的运行配置中选择Edit configuration点击进去：<br/>
![如图1](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
2)之后进入如下图所示，查看是否存在maven项目启动配置，不存在点击‘+’号进行添加<br/>
![如图2](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
![如图3](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
3)之后对执行应用进行命名，并配置执行文件：org.mortbay.jetty:maven-jetty-plugin:6.1.22:run<br/>
![如图4](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
4)如果对pom.xml配置的端口号很不爽的话，你也可以在这里的runner选项卡中进行覆盖：<br/>
![如图5](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
5)在控制台如果输出如下信息则表示jetty启动成功：<br/>
![如图6](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
6)在浏览器中进行访问http://localhost:8081/{你的项目名称}/:<br/>
![如图7](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>