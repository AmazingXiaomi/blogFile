---
title: java设计模式-适配器模式 
date: 2019-09-18 15:26:32
description: '适配器模式（Adapter），结构型模式,又叫包装模式。'
tags: java设计模式
---

# 适配器模式

## 1.简述
  **适配器模式**（Chain of Responsibility），把一个类的接口变换成客户端所期待的另一种接口，从而使原本接口不匹配而无法一起工作的两个类能够在一起工作
在现有的系统中有新旧两个接口，由于新旧接口不兼容导致客户端调用出现问题，但是现有系统还需要使用旧的接口，所以这个接口不能重构，但是为了能够让客户端正常调用，我们就需要将新的接口转换成旧的接口，这种解决方式就是适配器模式。
  
 共有两类适配器模式：
  **对象适配器模式**
      -- 在这种适配器模式中，适配器容纳一个它包裹的类的实例。在这种情况下，适配器调用被包裹对象的物理实体。
  **类适配器模式**
      -- 这种适配器模式下，适配器继承自已实现的类（一般多重继承）。

>  缺省适配器模式(Default Adapter Pattern)：当不需要实现一个接口所提供的所有方法时，可先设计一个抽象类实现该接口，并为接口中每个方法提供一个默认实现（空方法），那么该抽象类的子类可以选择性地覆盖父类的某些方法来实现需求，它适用于不想使用一个接口中的所有方法的情况，又称为单接口适配器模式。缺省适配器模式是适配器模式的一种变体，其应用也较为广泛。在JDK类库的事件处理包java.awt.event中广泛使用了缺省适配器模式，如WindowAdapter、KeyAdapter、MouseAdapter等。


   
## 2.案例实现
### 类适配器 
   ![img](https://github.com/AmazingXiaomi/image/blob/master/35A02604-41F4-4cd5-957F-3806B0CA88A4.png?raw=true)
   - **Target（目标抽象类）**：目标抽象类定义客户所需接口，可以是一个抽象类或接口，也可以是具体类。
   - **Adapter（适配器类）**：适配器可以调用另一个接口，作为一个转换器，对Adaptee和Target进行适配，适配器类是适配器模式的核心，在对象适配器中，它通过继承Target并关联一个Adaptee对象使二者产生联系。
   - **Adaptee（适配者类）**：适配者即被适配的角色，它定义了一个已经存在的接口，这个接口需要适配，适配者类一般是一个具体类，包含了客户希望使用的业务方法，在某些情况下可能没有适配者类的源代码。

**源代码**
````java
 public interface Target {
    public  void request();
    public void adapteeRequest();
}
````
上面的这个是目标角色的代码，他声明了两个方法， request()和adapteeRequest()，但是源角色Adaptee是一个具体类，adapteeRequest()方法，request()方法。

````java
public class Adaptee {
    public void adapteeRequest(){
        System.out.println("被适配的方法");
    }
}
````
适配器角色Adapter扩展了Adaptee,同时又实现了目标(Target)接口。由于Adaptee没有提供request()方法，而目标接口又要求这个方法，因此适配器角色Adapter实现了这个方法。
````java
public class Adapter extends Adaptee implements Target{
    @Override
    public void request() {
        //...一些操作...
    }
}
````
 
### 对象适配器  
  与类的适配器模式一样，对象的适配器模式把被适配的类的API转换成为目标类的API，与类的适配器模式不同的是，对象的适配器模式不是使用继承关系连接到Adaptee类，而是使用委派关系连接到Adaptee类。
  这里只需求修改一下Adapter代码即可。
````java
public class ObjectAdapter implements Target {
    Adaptee adaptee=new Adaptee();
    @Override
    public void request() {
        adaptee.adapteeRequest();
    }
}
````


### 缺省适配模式
  缺省适配(Default Adapter)模式为一个接口提供缺省实现，这样子类型可以从这个缺省实现进行扩展，而不必从原有接口进行扩展。
  作为适配器模式的一个特例，缺省是适配模式在JAVA语言中有着特殊的应用。
![img](https://github.com/AmazingXiaomi/image/blob/master/9E0BE79F-493E-4473-AB3A-A4CF39EBF855.png?raw=true) 
定义输出交流电接口，输出220V交流电类和输出110V交流电类
````java
public interface AC {
    int outPutAC();
}
public class AC110 implements AC {
    public final int output = 110;

    @Override
    public int outPutAC() {
        return output;
    }
}
public class AC220 implements AC {
    public final int output = 220;

    @Override
    public int outPutAC() {
        return output;
    }
}
````

  适配器接口，其中 support() 方法用于检查输入的电压是否与适配器匹配，outputDC5V() 方法则用于将输入的电压变换为 5V 后输出

```java
public interface DC5Adapter {
    boolean support(AC ac);
    int outputDC5V(AC ac);
}
```
  实现中国变压适配器和日本变压适配器
````java
public class ChinaPowerAdapter implements DC5Adapter {
    public static final int voltage = 220;

    @Override
    public boolean support(AC ac) {
        return voltage == ac.outPutAC();
    }

    @Override
    public int outputDC5V(AC ac) {
        int adapterInput = ac.outPutAC();
        //变压器...
        int adapterOutput = adapterInput / 44;
        System.out.println( "使用ChinaPowerAdapter变压适配器，输入AC:" + adapterInput + "V" + "，输出DC:" + adapterOutput + "V" );
        return adapterOutput;
    }
}
public class JapanPowerAdapter implements DC5Adapter {
    public static final int voltage = 110;

    @Override
    public boolean support(AC ac) {
        return voltage==ac.outPutAC();
    }

    @Override
    public int outputDC5V(AC ac) {
        int adapterInput = ac.outPutAC();
        //变压器...
        int adapterOutput = adapterInput / 22;
        System.out.println("使用JapanPowerAdapter变压适配器，输入AC:" + adapterInput + "V" + "，输出DC:" + adapterOutput + "V");
        return adapterOutput;
    }
}
````
测试，准备中国变压适配器和日本变压适配器各一个，定义一个方法可以根据电压找到合适的变压器，然后进行测试

````java
public class AdapterTest {
    private List<DC5Adapter> dc5Adapters=new ArrayList<>(  );

    public AdapterTest() {
        this.dc5Adapters.add( new ChinaPowerAdapter() );
        this.dc5Adapters.add( new JapanPowerAdapter() );
    }

    public DC5Adapter getPowerAdapter(AC  ac){
        DC5Adapter adapter = null;
        for (DC5Adapter item : dc5Adapters) {
            if (item.support( ac )) {
                adapter = item;
                break;
            }
        }
        if (adapter==null){
            System.out.println("没有找到合适的适配器！");
        }
        return adapter;
    }

    public static void main(String[] args) {
        AdapterTest test = new AdapterTest();
        AC chinaAC = new AC220();
        DC5Adapter adapter = test.getPowerAdapter(chinaAC);
        adapter.outputDC5V(chinaAC);

        AC japanAC = new AC110();
        adapter=test.getPowerAdapter( japanAC );
        adapter.outputDC5V( japanAC );
    }
}
````
**输出**
````cfml
使用ChinaPowerAdapter变压适配器，输入AC:220V，输出DC:5V
使用JapanPowerAdapter变压适配器，输入AC:110V，输出DC:5V
````
## 3.总结
### 优点
1. 将目标类和适配者类解耦，通过引入一个适配器类来重用现有的适配者类，无须修改原有结构。
2. 增加了类的透明性和复用性，将具体的业务实现过程封装在适配者类中，对于客户端类而言是透明的，而且提高了适配者的复用性，同一个适配者类可以在多个不同的系统中复用。
3. 灵活性和扩展性都非常好，通过使用配置文件，可以很方便地更换适配器，也可以在不修改原有代码的基础上增加新的适配器类，完全符合“开闭原则”。

具体来说，类适配器模式还有如下优点：
 - 由于适配器类是适配者类的子类，因此可以在适配器类中置换一些适配者的方法，使得适配器的灵活性更强。
 
对象适配器模式还有如下优点：
 - 一个对象适配器可以把多个不同的适配者适配到同一个目标；
 - 可以适配一个适配者的子类，由于适配器和适配者之间是关联关系，根据“里氏代换原则”，适配者的子类也可通过该适配器进行适配。
 
### 缺点
类适配器模式的缺点如下：

 - 对于Java、C#等不支持多重类继承的语言，一次最多只能适配一个适配者类，不能同时适配多个适配者；
 - 适配者类不能为最终类，如在Java中不能为final类，C#中不能为sealed类；
 - 在Java、C#等语言中，类适配器模式中的目标抽象类只能为接口，不能为类，其使用有一定的局限性。
 
对象适配器模式的缺点如下：

 - 与类适配器模式相比，要在适配器中置换适配者类的某些方法比较麻烦。如果一定要置换掉适配者类的一个或多个方法，可以先做一个适配者类的子类，将适配者类的方法置换掉，然后再把适配者类的子类当做真正的适配者进行适配，实现过程较为复杂。


### 适用场景

 - 系统需要使用一些现有的类，而这些类的接口（如方法名）不符合系统的需要，甚至没有这些类的源代码。
 - 想创建一个可以重复使用的类，用于与一些彼此之间没有太大关联的一些类，包括一些可能在将来引进的类一起工作。
