---
title:  java动态代理学习
date: 2018-01-11 13:27:32
description: 'java代理模式学习'
tags: java
---


# 第一章   java代理模式

**代理模式的定义**：给某一个对象提供一个代理，并由代理对象控制对原对象的引用。

## 
``` bash
$ 静态代理
```

自己编写代理类，在程序运行前就编译好的。

创建接口：Hello
```code
package com.xiaomi.proxy;

/**
 * Created by xiaomi on 2018/1/11 0011.
 */
public interface Hello {
    public String sayHello(String hello);
}
```

定义它的实现类
```code
package com.xiaomi.proxy.impl;

import com.xiaomi.proxy.Hello;

/**
 * Created by xiaomi on 2018/1/11 0011.
 * .
 */
public class HelloImpl implements Hello{
    @Override
    public String sayHello(String hello) {
        return "HelloImpl"+hello;
    }
}
```

如何实现静态代理呢？
这是Java种再常见不过的场景，使用接口制定协议，然后用不同的实现来实现具体行为。假设你已经拿到上述类库，如果我们想通过日志记录对sayHello()的调用，使用静态代理可以这样做：
```code
package com.xiaomi.proxy.realize;

import com.xiaomi.proxy.Hello;
import com.xiaomi.proxy.impl.HelloImpl;

/**
 * Created by xiaomi on 2018/1/11 0011.
 * .
 */
public class StaticHelloProxy implements Hello{
    /**
     * java proxy 静态代理
     */
    private Hello hellow=new HelloImpl();
    @Override
    public String sayHello(String hello) {
        return hellow.sayHello(hello);
    }
}
```


***

``` bash
$ java proxy动态代理
```

上例中静态代理类StaticProxiedHello作为HelloImp的代理，实现了相同的Hello接口。用Java动态代理可以这样做：

1. 首先实现一个InvocationHandler，方法调用会被转发到该类的invoke()方法。
2. 然后在需要使用Hello的时候，通过JDK动态代理获取Hello的代理对象。

```code
package com.xiaomi.proxy.realize;

import com.xiaomi.proxy.Hello;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.util.Arrays;
import java.util.logging.Logger;

/**
 * Created by xiaomi on 2018/1/11 0011.
 *
 */
public class LogInvocationHandler implements InvocationHandler{
    final  static Logger log = Logger.getLogger("xiaomi");

    private Hello hello;

    public LogInvocationHandler(Hello hello) {
        this.hello = hello;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println(method.getName());
        if("sayHello".equals(method.getName())) {
            log.info("You said: " + Arrays.toString(args));
        }
        return method.invoke(hello, args);
    }

}

```

然后在需要调用动态代理的地方加入
```code
Hello hello = (Hello) Proxy.newProxyInstance(
            getClass().getClassLoader(),
            new Class<?>[] {Hello.class},
            new LogInvocationHandler(new HelloImpl()));
        return hello.sayHello(hello1);
```
即可完成<code>sayHello()</code>方法的动态代理
***
``` bash
 $ CGLIB动态代理
```
CGLIB(Code Generation Library)是一个基于ASM的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成。CGLIB通过继承方式实现代理。

假设我们有一个未实现任何接口的类:HelloCocreate
```code
package com.xiaomi.proxy;

/**
 * Created by xiaomi on 2018/1/11 0011.
 *
 * @author xiaomi
 * @email a1205938863@gmail.com
 */
public class HelloCocreate {
    public String sayhello(String hello){
        return hello;
    }
}
```
因为这个类未实现任何接口，所以无法使用java动态代理,下面使用CGLIB动态代理：
1. 首先实现一个MethodInterceptor，方法调用会被转发到该类的intercept()方法。
2. 然后在需要使用HelloConcrete的时候，通过CGLIB动态代理获取代理对象。

```code
package com.xiaomi.proxy.realize;

import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;
import java.util.Arrays;
import java.util.logging.Logger;

/**
 * Created by xiaomi on 2018/1/11 0011.
 *
 * @author xiaomi
 * @email a1205938863@gmail.com
 */
public class MyMethodInterceptor implements MethodInterceptor {
    final  static Logger logger = Logger.getLogger("xiaomi");

    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println(method.getName());
        logger.info("You said: " + Arrays.toString(objects));
        return methodProxy.invokeSuper(o, objects);
    }
}

```

然后在需要使用HelloConcrete的时候，通过CGLIB动态代理获取代理对象。

```code
package com.xiaomi.proxy.realize;

import com.xiaomi.proxy.Hello;
import com.xiaomi.proxy.HelloCocreate;
import net.sf.cglib.proxy.Enhancer;


/**
 * Created by xiaomi on 2018/1/11 0011.
 * .
 */
public class StaticHelloProxy implements Hello{
    @Override
    public String sayHello(String hello) {

        /**
         *         cglib动态代理
         */
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(HelloCocreate.class);
        enhancer.setCallback(new MyMethodInterceptor());
        HelloCocreate cocreate=  (HelloCocreate)enhancer.create();
        return cocreate.sayhello(hello);
    }

}
```

上述代码中，我们通过CGLIB的<code>Enhancer</code>来指定要代理的目标对象、实际处理代理逻辑的对象，最终通过调用create()方法得到代理对象，对这个对象所有非final方法的调用都会转发给MethodInterceptor.intercept()方法，在intercept()方法里我们可以加入任何逻辑，比如修改方法参数，加入日志功能、安全检查功能等；通过调用MethodProxy.invokeSuper()方法，我们将调用转发给原始对象，具体到本例，就是HelloConcrete的具体方法。CGLIG中MethodInterceptor的作用跟JDK代理中的InvocationHandler很类似，都是方法调用的中转站。
*** 注意：对于从Object中继承的方法，CGLIB代理也会进行代理，如hashCode()、equals()、toString()等，但是getClass()、wait()等方法不会，因为它是final方法，CGLIB无法代理。

