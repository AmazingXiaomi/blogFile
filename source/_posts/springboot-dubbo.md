---
title: springboot与dubbo整合
date: 2017-11-8 22:43:32
tags: springboot
description: '这里配置的是单机模式下的zookeeper，如果搭建的是分布式的服务器的话还可以搭建集群模式。'
---


* * *

##	dubbo服务搭建
```bash
搭建zookeeper 步骤
```
这里配置的是单机模式下的zookeeper，如果搭建的是分布式的服务器的话还可以搭建集群模式。
1. 从官网下载压缩包。下载地址[zookeeper下载](http://zookeeper.apache.org/releases.html)
2. 解压缩下载好的文件，进入conf文件夹内，创建zoo.cfg文件。并写入以下内容。
```code
# The number of milliseconds of each tick  
tickTime=2000
# The number of ticks that the initial   
# synchronization phase can take  
initLimit=10
# The number of ticks that can pass between   
# sending a request and getting an acknowledgement  
syncLimit=5 
# the directory where the snapshot is stored.  
# do not use /tmp for storage, /tmp here is just   
# example sakes.  
dataDir=E:\\zookeeper-3.4.10\\zookeeper-3.4.10\\data 
dataLogDir=E:\\zookeeper-3.4.10\\zookeeper-3.4.10\\log
# the port at which the clients will connect  
clientPort=2181
```

3. linux系统进入bin目录下，启动zookeeper，输入以下命令完成启动。
<code>
./zkServer.sh start
</code>

```bash
安装dubbo服务 步骤
```
1. 下载dubbo的项目下来，可从[dubbo官网](http://dubbo.io/)下载到本地。解压文件。
2. 进入dubbo-admin的目录下,打成war包。输入如下命令进入target即可看到war包。
<pre><code>
mvn clean package
<code></pre>

3. 拷贝war包到tomcat中，启动tomcat，修改WEB—INF下的dubbo.properties文件，将zookeeper改成自己的zookeeper的地址，修改root的guest的密码。重新启动tomcat即可。


完成以上安装，在浏览器输入localhost:8080即可进入dubbo管理中心。
注意：需要将dubbo-admin项目修改为tomcat的root根路径中，有两种方式可以修改。
>1. 直接将项目拷贝到root目录下。
>2. 修改tomcat的server.xml 具体如下
```code
	<Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
		<Context path="" docBase="dubbo-admin-2.5.5" reloadable="true" />
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
```

## 在springboot中添加dubbo服务

```bash
加入dubbo的依赖
```
```code
 <dependency>
            <groupId>io.dubbo.springboot</groupId>
            <artifactId>spring-boot-starter-dubbo</artifactId>
            <version>1.0.0</version>
        </dependency>
```
```bash
加入dubbo的配置文件
```

```code
spring.dubbo.application.name=provider
spring.dubbo.registry.address=zookeeper://47.89.17.7:2181
spring.dubbo.protocol.name=dubbo
spring.dubbo.protocol.port=20880
spring.dubbo.scan=com.xiaomi.springboot.dubbo
```

这个时候只要在com.xiaomi.springboot.dubbo包中的接口加上dubbo的service注解即可将dubbo服务暴露出来。

## 动手搭建一个完整的dubbo服务
```bash
分别创建两个springboot的model，一个作为privider端，一个作为cusome端。
```
分别加入上面的dubbo依赖和配置文件。具体代码见下面几张图，

![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/dubboClient.png)
![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/dubboServer.png)


CityDubboService类
```code
package com.xiaomi.springboot.dubbo;


import com.xiaomi.springboot.domain.City;

/**
 * 城市业务 Dubbo 服务层
 *
 * Created by bysocket on 28/02/2017.
 */
public interface CityDubboService {

    /**
     * 根据城市名称，查询城市信息
     * @param cityName
     */
    City findCityByName(String cityName);
}
```
** CityDubboServiceImpl类 **
```code
package com.xiaomi.springboot.dubbo.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.xiaomi.springboot.domain.City;
import com.xiaomi.springboot.dubbo.CityDubboService;

/**
 * 城市业务 Dubbo 服务层实现层
 *
 * Created by bysocket on 28/02/2017.
 */
// 注册为 Dubbo 服务
@Service(version = "1.0.0")
public class CityDubboServiceImpl implements CityDubboService {

    public City findCityByName(String cityName) {
        return new City(1L,2L,"温岭","是我的故乡");
    }
}

```



City类
```code
package com.xiaomi.springboot.domain;

import java.io.Serializable;

/**
 * 城市实体类
 *
 * Created by xiaomi on 07/02/2017.
 */
public class City implements Serializable {

    private static final long serialVersionUID = -1L;

    /**
     * 城市编号
     */
    private Long id;

    /**
     * 省份编号
     */
    private Long provinceId;

    /**
     * 城市名称
     */
    private String cityName;

    /**
     * 描述
     */
    private String description;

    public City() {
    }

    public City(Long id, Long provinceId, String cityName, String description) {
        this.id = id;
        this.provinceId = provinceId;
        this.cityName = cityName;
        this.description = description;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public Long getProvinceId() {
        return provinceId;
    }

    public void setProvinceId(Long provinceId) {
        this.provinceId = provinceId;
    }

    public String getCityName() {
        return cityName;
    }

    public void setCityName(String cityName) {
        this.cityName = cityName;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}

```
CityDubboConsumerService类
```code
package com.xiaomi.springboot.dubbo;

import com.alibaba.dubbo.config.annotation.Reference;
import com.xiaomi.springboot.domain.City;
import org.springframework.stereotype.Component;

import java.util.List;

/**
 * 城市 Dubbo 服务消费者
 *
 * Created by bysocket on 28/02/2017.
 */
@Component
public class CityDubboConsumerService {

    @Reference(version = "1.0.0")
    CityDubboService cityDubboService;

    @Reference(version = "1.0.0")
    AllCityService cityService;

    public void printCity() {
        String cityName="温岭";
        City city = cityDubboService.findCityByName(cityName);
        System.out.println(city.toString());
    }

    public void printCitys() {
        List<City> allCity = cityService.findAllCity();
        System.out.println(allCity.toString());
    }
}

```

消费者启动时测试代码

```code
package com.xiaomi.springboot;

import com.xiaomi.springboot.dubbo.CityDubboConsumerService;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

/**
 * Spring Boot 应用启动类
 *
 * Created by bysocket on 16/4/26.
 */
// Spring Boot 应用的标识
@SpringBootApplication
public class ClientApplication {

    public static void main(String[] args) {
        // 程序启动入口
        // 启动嵌入式的 Tomcat 并初始化 Spring 环境及其各 Spring 组件
        ConfigurableApplicationContext run = SpringApplication.run(ClientApplication.class, args);
        CityDubboConsumerService cityService = run.getBean(CityDubboConsumerService.class);
        cityService.printCitys();
    }
}
```

可以看到如下结果

![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/dubboResult.png)

大功告成！！！
