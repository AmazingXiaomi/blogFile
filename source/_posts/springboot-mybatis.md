---
title: springboot与mybatis整合
date: 2017-10-30 22:43:32
tags: springboot
description: 'Springboot 相关，一方面和大家交流交流 Springboot 框架。'
---

# springboot与mybatis整合

Springboot 相关，一方面和大家交流交流 Springboot 框架。
现在业界互联网流行的数据操作层框架 Mybatis，下面详解下 Springboot 如何整合 Mybatis ，这边没有使用 Mybatis Annotation 这种，
是使用 xml 配置 SQL。因为我觉得 SQL 和业务代码应该隔离，方便和 DBA 校对 SQL。二者 XML 对较长的 SQL 比较清晰。

## 项目搭建

1. 首先修改pom文件，引入mybatis的依赖。
```code
  <!-- Spring Boot Web 依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Boot Test 依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- Spring Boot Mybatis 依赖 -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>${mybatis-spring-boot}</version>
        </dependency>

        <!-- MySQL 连接驱动依赖 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql-connector}</version>
        </dependency>
```

2. 在application的配置文件中添加mysql连接的配置文件
<pre><code>
spring.datasource.url=jdbc:mysql://localhost:3306/springbootdb?useUnicode=true&characterEncoding=utf8&useSSL=false
spring.datasource.username=root
spring.datasource.password=xiaomi
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
## Mybatis 配置
mybatis.typeAliasesPackage=com.xiaomi.spring.domain
mybatis.mapperLocations=classpath:mapper/*.xml
<code></pre>


3. 在 Application 应用启动类添加注解 MapperScan
```code
@SpringBootApplication
@MapperScan("com.xiaomi.spring.dao")
public class Application {

    public static void main(String[] args) {
        // 程序启动入口
        // 启动嵌入式的 Tomcat 并初始化 Spring 环境及其各 Spring 组件
        SpringApplication.run(Application.class,args);
    }
}
```

后续添加