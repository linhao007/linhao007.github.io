---
layout: post
title:  "��spring boot�������jetty������"
date:   2015-12-14 14:06:05
categories: ��ҵ���
excerpt: ��IntelliJ IDEA��maven��Ŀ���ʹ��jetty����
---

* content
{:toc}

## ǰ�� 
  &nbsp;&nbsp;&nbsp;&nbsp;��ǰһֱʹ��eclipse + tomcat��������Ŀ������������һ����Ŀ��ʱ��ѧ�������ˣ�
  ��˵��tomcat����������������һ��jetty������web��Ŀ,���������˽���һ��jetty.<br/>
  
## ǳ̸jetty
&nbsp;&nbsp;&nbsp;&nbsp;Jetty ��һ����Դ��servlet��������Ϊ����Java��web����������JSP��servlet�ṩ���л�����Jetty��ʹ��Java���Ա�д�ģ�����API��һ��JAR������ʽ������
������Ա���Խ�Jetty����ʵ������һ�����󣬿���Ѹ��ΪһЩ�������У�stand-alone����JavaӦ���ṩ�����web���ӡ�[ժ�԰ٶȰٿ�](http://baike.baidu.com/link?url=qISf-60N2e1v1DqK60Z5ZOLgZw_EM38M59mhbfzBAZqkIg1o0PPKsIsDD80Q4K14SeoRRBEpieEtWQTWdsXDpq) <br/>


## jetty��tomcat������
###��ͬ�㣺
&nbsp;&nbsp;&nbsp;&nbsp;1.      Tomcat��Jetty����һ��Servlet���棬���Ƕ�֧�ֱ�׼��servlet�淶��JavaEE�Ĺ淶��<br/>
 
 
### ��ͬ�㣺
&nbsp;&nbsp;&nbsp;&nbsp;1.      �ܹ��Ƚ�<br/>
Jetty�ļܹ���Tomcat�ĸ�Ϊ��<br/>
Jetty�ļܹ��ǻ���Handler��ʵ�ֵģ���Ҫ����չ���ܶ�������Handler��ʵ�֣���չ�򵥡�<br/>
Tomcat�ļܹ��ǻ���������Ƶģ�������չ����Ҫ�˽�Tomcat��������ƽṹ��������չ��<br/><br/>

&nbsp;&nbsp;&nbsp;&nbsp;2.      ���ܱȽ�<br/>
Jetty��Tomcat���ܷ�����첻��<br/>
Jetty����ͬʱ����������Ӷ��ҿ��Գ�ʱ�䱣�����ӣ��ʺ���web����Ӧ�õȵȡ�<br/>
Jetty�ļܹ��򵥣������Ϊ��������Jetty���԰��������������ٲ���Ҫ������������˷������ڴ濪�����Ӷ���߷��������ܡ�<br/>
JettyĬ�ϲ���NIO�����ڴ���I/O�����ϸ�ռ���ƣ��ڴ���̬��Դʱ�����ܽϸ�<br/><br/>
 
Tomcat�ʺϴ��������ǳ���æ�����ӣ�Ҳ����˵�����������ڶ̵Ļ���Tomcat���������ܸ��ߡ�<br/>
TomcatĬ�ϲ���BIO����I/O�����ڴ���̬��Դʱ�����ܽϲ<br/>
 
&nbsp;&nbsp;&nbsp;&nbsp;3.      �����Ƚ�<br/>
Jetty��Ӧ�ø��ӿ��٣��޸ļ򵥣����µ�Servlet�淶��֧�ֽϺã����ԱȽ�����ҵ�Ļ�ӭ��<br/>
TomcatĿǰӦ�ñȽϹ㷺����JavaEE��Servlet��֧�ָ���ȫ�棬�ܶ����Ի�ֱ�Ӽ��ɽ�����<br/>

## IntelliJ IDEA��maven��Ŀ���ʹ��jetty����
1����������Ҫ��maven�а�װjetty�������ʵ��������һ��jar��,������idea���м��ɣ�������ֻ��Ҫ��pom.xml�н����������ã�</br>
<pre><code class="markdown">
 <!--jetty���-->
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
                        <!--�����Զ���˿ںţ�Ҳ������idea�Լ����ã�����ὲ�������idea�����ö˿ں�-->
                        <connector implementation="org.eclipse.jetty.server.nio.SelectChannelConnector">
                            <port>8086</port>
                        </connector>
                    </connectors>
                </configuration>
            </plugin>
</code></pre><br/>

2��֮������jetty������������jar��<br/>
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

3����������IDEA�н���MAVEN��Ŀjetty�������ã�<br/>
1)������IDEA�е�����������ѡ��Edit configuration�����ȥ��<br/>
![��ͼ1](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
2)֮���������ͼ��ʾ���鿴�Ƿ����maven��Ŀ�������ã������ڵ����+���Ž������<br/>
![��ͼ2](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
![��ͼ3](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
3)֮���ִ��Ӧ�ý���������������ִ���ļ���org.mortbay.jetty:maven-jetty-plugin:6.1.22:run<br/>
![��ͼ4](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
4)�����pom.xml���õĶ˿ںźܲ�ˬ�Ļ�����Ҳ�����������runnerѡ��н��и��ǣ�<br/>
![��ͼ5](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
5)�ڿ���̨������������Ϣ���ʾjetty�����ɹ���<br/>
![��ͼ6](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>
6)��������н��з���http://localhost:8081/{�����Ŀ����}/:<br/>
![��ͼ7](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)<br/>