---
title: Intellij IDEA使用Maven快速创建Spring MVC Web项目工程
author: 谇雨
layout: post
permalink: /intellij-create-spring-mvc-project-by-maven.html
categories:
  - Java
tags:
  - Spring
  - Maven
  - Intellij
---

最近团队正在做一些Java的转型，在一段时间的编程过程中，发现Java配合Maven的XML配置蛮多的，自己在调试的过程中也是踩了不少的坑，期间遇到各种问题，搜索的网上大量的文章，虽然大部分都是互相转载，加之自己摸爬滚打，周围有经验丰富的java工程师，综合下来也解决了大部分问题，正好十一放假，根据最近使用 Intellij 的经验做一些总结和记录，以免将来少走弯路。

## 创建项目以及工程

在 Intellij 创建项目，选择 Maven 工程 webapp，如下图：

![创建项目]({{ site.url }}/uploads/2016/10/intellij-create-project.png)

创建模块，可以简单先填写，这里方便演示，举个例子使用 demo.api，表示demo项目的api模块(创建结束后也可以修改和增加模块)

![创建模块]({{ site.url }}/uploads/2016/10/intellij-create-module.png)

<!--more-->

## 设置配置文件

耐心等待项目文件创建以及加载完成后，可见 Intellij 左侧的目录树结构
    
    src
        main
            resourses
            webapp
                WEB-INF
                    web.xml
                index.jsp
    crackedzone.com.iml
    pom.xml

先在目录 src/main/ 中创建文件夹 java 用于将来编写程序  
创建完成，发现这个 java 文件夹没有被 Intellij 识别，我们需要在 crackedzone.com.iml 做如下设置：

    <content url="file://$MODULE_DIR$">
        <sourceFolder url="file://$MODULE_DIR$/src/main/java" isTestSource="false" />
        <sourceFolder url="file://$MODULE_DIR$/src/main/resources" type="java-resource" />
        <excludeFolder url="file://$MODULE_DIR$/target" />
    </content>

保存后，项目中的icon将会发变化，说明 java, resources 文件夹可以被 Intellij 识别  
可以开始在 Maven 提供的 pom 文件(pom.xml) 添加我们spring的依赖，设置 pom.xml 前，需要到项目设置中 Maven autoload 勾选，否则无法 autoload maven 依赖库(这是Intellij的坑，找了很久才找到)

![maven 自动装载]({{ site.url }}/uploads/2016/10/intellij-maven-auto-import.png)

勾选后，可以在 pom.xml 添加 dependencies, plugins，以下是 pom.xml 设置后的完整文件

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>com.crackedzone</groupId>
        <artifactId>demo.api</artifactId>
        <packaging>war</packaging>
        <version>1.0-SNAPSHOT</version>
        <name>demo.api Maven Webapp</name>
        <url>http://maven.apache.org</url>
        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
                <version>4.2.6.RELEASE</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>4.2.6.RELEASE</version>
            </dependency>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>jstl</artifactId>
                <version>1.2</version>
            </dependency>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>javax.servlet-api</artifactId>
                <version>3.0.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>javax.servlet.jsp</groupId>
                <artifactId>jsp-api</artifactId>
                <version>2.2</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
        <build>
            <finalName>demo.api</finalName>
            <plugins>
                <plugin>
                    <groupId>org.apache.tomcat.maven</groupId>
                    <artifactId>tomcat7-maven-plugin</artifactId>
                    <version>2.2</version>
                    <configuration>
                        <port>8080</port>
                        <path>/</path>
                        <uriEncoding>utf-8</uriEncoding>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>

说明几点：

1. Maven plugin tomcat 8080 端口来启动Web服务；
2. 保存后，需要等待 Maven 依赖库下载以及装载完毕后，编辑器会提示下载完成，加载完成，左边的 External Libraries 将会出现依赖库；

打开 src/main/webapp/WEB-INF/web.xml，该 xml 是web工程的配置，这里需要添加上我们 spring.xml 的配置信息，当然还没创建，我们定义为 spring.xml ，也放置在 WEB-INF 下，web.xml 程序如下

    <!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd" >

    <web-app>
        <display-name>Archetype Created Web Application</display-name>
        <servlet>
            <servlet-name>mvc-dispatcher</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>/WEB-INF/spring.xml</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <servlet-name>mvc-dispatcher</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>
    </web-app>

同时创建一个 spring.xml，程序如下

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-3.2.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-3.2.xsd">

        <context:annotation-config/>

        <context:component-scan base-package="com.crackedzone.demo.controller">
            <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        </context:component-scan>

        <mvc:annotation-driven/>

        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
            <property name="prefix" value="/"/>
            <property name="suffix" value=".jsp"/>
        </bean>
    </beans>

在 spring.xml 中描述几块：

1. 自动扫描Annotation；
2. 扫描控制器在 com.crackedzone.demo.controller 包下
3. 使用视图解析，目录在 webapp 根目录下，扩展名 .jsp

## 编写Java程序

自此，我们终于可以开始编写我们的 java 控制器，在 java 目录下创建 pacakge: com.crackedzone.demo.controller, 添加一个 Hello 类，代码如下

    package com.crackedzone.demo.controller;

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;
    import org.springframework.web.bind.annotation.ResponseBody;

    import javax.servlet.http.HttpServletRequest;

    /**
     * Package com.crackedzone.demo.controller
     *
     * @author Lancer He <lancer.he@gmail.com>
     */
    @Controller
    @RequestMapping("/hello")
    public class Hello {

        @RequestMapping(value = "/index", method = RequestMethod.GET)
        public String index(HttpServletRequest request) {
            return "index";
        }
    }

此代码需要说明一下：

1. 注解 @Controller 表示这是一个控制器，当请求来时将会扫描是否有匹配的RequestMapping
2. 注解 @RequestMapping 表示映射的路由，这里表示的是 /hello/index
3. return index 表示使用的模版是index，也就是 webapp/index.jsp

可以开始配置运行：

![maven 自动装载]({{ site.url }}/uploads/2016/10/intellij-maven-run.png)

配置无误后，运行将会看到一堆红色的运行结果，当你看到指定的端口提示后，说明已经运行成功：

    /Library/Java/JavaVirtualMachines/jdk1.8.0_73.jdk/Contents/Home/bin/java "-Dmaven.home=/Applications/IntelliJ IDEA 15 CE.app/Contents/plugins/maven/lib/maven3" "-Dclassworlds.conf=/Applications/IntelliJ IDEA 15 CE.app/Contents/plugins/maven/lib/maven3/bin/m2.conf" -Didea.launcher.port=7533 "-Didea.launcher.bin.path=/Applications/IntelliJ IDEA 15 CE.app/Contents/bin" -Dfile.encoding=UTF-8 -classpath "/Applications/IntelliJ IDEA 15 CE.app/Contents/plugins/maven/lib/maven3/boot/plexus-classworlds-2.4.jar:/Applications/IntelliJ IDEA 15 CE.app/Contents/lib/idea_rt.jar" com.intellij.rt.execution.application.AppMain org.codehaus.classworlds.Launcher -Didea.version=15.0.4 tomcat7:run
    [INFO] Scanning for projects...
    [INFO]                                                                         
    [INFO] ------------------------------------------------------------------------
    [INFO] Building demo.api Maven Webapp 1.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    ....
    信息: Looking for @ControllerAdvice: WebApplicationContext for namespace 'mvc-dispatcher-servlet': startup date [Fri Oct 07 16:29:31 HKT 2016]; root of context hierarchy
    十月 07, 2016 4:29:32 下午 org.springframework.web.servlet.DispatcherServlet initServletBean
    信息: FrameworkServlet 'mvc-dispatcher': initialization completed in 1064 ms
    十月 07, 2016 4:29:32 下午 org.apache.coyote.AbstractProtocol start
    信息: Starting ProtocolHandler ["http-bio-8080"]

通过浏览器访问 http://localhost:8080/hello/index, 将会看到 Hello World! 表示已经成功

## 编写Java API

对于Web Api的工程，我们需要返回 JSON 结构数据，为了程序方便处理，我们现在 dependencies 中增加一个 json 的依赖库

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.17</version>
    </dependency>

给 Hello.java 增加一个方法：

    package com.crackedzone.demo.controller;

    import com.alibaba.fastjson.JSON;
    import com.alibaba.fastjson.serializer.SerializerFeature;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;
    import org.springframework.web.bind.annotation.ResponseBody;

    import javax.servlet.http.HttpServletRequest;
    import java.util.HashMap;

    /**
     * Package com.crackedzone.demo.controller
     *
     * @author Lancer He <lancer.he@gmail.com>
     */
    @Controller
    @RequestMapping("/hello")
    public class Hello {

        @RequestMapping(value = "/index", method = RequestMethod.GET)
        public String index(HttpServletRequest request) {
            return "index";
        }


        @RequestMapping(value = "/json", method = RequestMethod.GET, produces = "application/json; charset=utf-8")
        @ResponseBody
        public String json(HttpServletRequest request) {
            HashMap<String, Object> responseBody = new HashMap<String, Object>();
            responseBody.put("code", 200);
            responseBody.put("message", "Create success.");
            return JSON.toJSONString(responseBody, SerializerFeature.WriteNonStringValueAsString);
        }
    }

1. 注解 @ResponseBody 将会把 return string 放置在 http response body 中
2. produces = "application/json; charset=utf-8" 表示返回的header头格式

重新运行，访问 http://localhost:8080/hello/json 将会看到：

    {
        "code": 200,
        "message": "Create success."
    }

至此完成搭建一个简单的 web 项目。
