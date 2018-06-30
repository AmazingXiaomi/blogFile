---
title: Restful学习
date: 2017-11-6 22:43:32
tags: springboot
description: 'Springboot 实现 Restful 服务，基于 HTTP / JSON 传输。'
---

# Springboot 实现 Restful 服务，基于 HTTP / JSON 传输

首先看一下具体代码模块。。。

## 基于上一篇的springboot与mybatis结合。在新建控制器CityRestController


```code
package org.spring.springboot.controller;

import org.spring.springboot.domain.City;
import org.spring.springboot.service.CityService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * 城市 Controller 实现 Restful HTTP 服务
 *
 * Created by bysocket on 07/02/2017.
 */
@RestController
public class CityRestController {

    @Autowired
    private CityService cityService;

    @RequestMapping(value = "/api/city/{id}", method = RequestMethod.GET)
    public City findOneCity(@PathVariable("id") Long id) {
        return cityService.findCityById(id);
    }

    @RequestMapping(value = "/api/city", method = RequestMethod.GET)
    public List<City> findAllCity() {
        return cityService.findAllCity();
    }

    @RequestMapping(value = "/api/city", method = RequestMethod.POST)
    public void createCity(@RequestBody City city) {
        cityService.saveCity(city);
    }

    @RequestMapping(value = "/api/city", method = RequestMethod.PUT)
    public void modifyCity(@RequestBody City city) {
        cityService.updateCity(city);
    }

    @RequestMapping(value = "/api/city/{id}", method = RequestMethod.DELETE)
    public void modifyCity(@PathVariable("id") Long id) {
        cityService.deleteCity(id);
    }
}
```

``` bash
$ 打开postman进行测试
```

* get请求

![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/get123.png)
*  POST请求

![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/post123.png)
*  PUSH请求

![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/push123.png)
*  DELETE请求

![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/delete123.png)

* * *

## springboot-restful 工程控制层实现详解

```bash
*1. 什么是Restful*
```
REST 是属于 WEB 自身的一种架构风格，是在 HTTP 1.1 规范下实现的。Representational State Transfer 全称翻译为表现层状态转化。Resource：资源。比如 newsfeed；Representational：表现形式，比如用JSON，富文本等；State Transfer：状态变化。通过HTTP 动作实现。
理解 REST ,要明白五个关键要素：

>资源（Resource）
>资源的表述（Representation）
>状态转移（State Transfer）
>统一接口（Uniform Interface）
>超文本驱动（Hypertext Driven）

详见 [restful详解](http://www.infoq.com/cn/articles/understanding-restful-style)


**代码详解：**

>@RequestMapping 处理请求地址映射。
>method - 指定请求的方法类型：POST/GET/DELETE/PUT 等
>value - 指定实际的请求地址
>consumes - 指定处理请求的提交内容类型，例如 Content-Type 头部设置application/json, text/html
>produces - 指定返回的内容类型

@PathVariable URL 映射时，用于绑定请求参数到方法参数
@RequestBody 这里注解用于读取请求体 boy 的数据，通过 HttpMessageConverter 解析绑定到对象中