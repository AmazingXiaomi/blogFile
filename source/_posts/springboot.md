---
title:  springboot入门
date: 2017-08-14 22:43:32
description: 'springboot能够便捷的创建spring项目，减少了很多配置。下面有两种创建方式'
tags: springboot
---


# 第一章   springboot入门

## springboot能够便捷的创建spring项目，减少了很多配置。下面有两种创建方式。
``` bash
$ 官网上直接下载
```

可从[springboot](http://projects.spring.io/spring-boot/) 官网下载设置自己需要的项目。
这里不再详细讲解。

``` bash
$ 本地创建mvn项目然后继承springboot项目即可。
```

首先得有个maven基础项目，可以直接使用Maven骨架工程生成Maven骨架Web项目，即man archetype:generate命令：
<pre><code>mvn archetype:generate -DgroupId=springboot -DartifactId=springboot-helloworld -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false</code></pre>

## 配置pom

主要的代码就是继承springboot加入springboot依赖。

```code  
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>springboot</groupId>
    <artifactId>springboot-helloworld</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-helloworld :: HelloWorld Demo</name>
    <!-- Spring Boot 启动父依赖 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.3.RELEASE</version>
    </parent>
    <dependencies>
        <!-- Spring Boot web依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- Junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
</project>
```

---

# springboot 第一个测试类

目录结构如下：
![项目结构图](https://raw.githubusercontent.com/AmazingXiaomi/image/master/jietu1.png)

1. 首先创建一个包，然后新建一个启动类。
```
package com.xiaomi.spring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * Hello world!
 * springboot 启动类
 */
//
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        System.out.println("Hello World!");

//程序启动入口
        //启动嵌入式的tomcat并初始化spring环境及各个spring组件
        SpringApplication.run(App.class, args);

    }
}
```
这样运行这个类，我们的项目就运行起来了。

2. 创建一个控制器来测试运行的结果。
```

package com.xiaomi.spring.web;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created by xiaolai on 2017/10/23.
 */
@RestController
public class TestController {
    @RequestMapping(value = "/")
    public String test(){
        return "HelloWorld";
    }
}
```
***特别注意***  所有的控制器和服务层必须和运行主类在同一个包下，而且在主类的子包里面。





