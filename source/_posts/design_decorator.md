---
title: java设计模式-装饰器模式 
date: 2019-09-06 14:03:32
description: '装饰器模式（decorator pattern）：允许向一个先有的对象增添新的功能，同时又不改变其解构。'
tags: java设计模式
---

# 装饰器模式

## 1.简述
 **装饰器模式**（Decorator Pattern）允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。
 
 这种模式创建了一个装饰类，用来包装原有的类，并在保持类方法签名完整性的前提下，提供了额外的功能。
 
**意图**：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。

**主要解决**：一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。

**何时使用**：在不想增加很多子类的情况下扩展类。

**如何解决**：将具体功能职责划分，同时继承装饰者模式。

![img](https://github.com/AmazingXiaomi/image/blob/master/815220-20170416031217864-1019073972.png?raw=true)

以上为装饰器模式的通用类图：
　　**Component**，一般是接口或者抽象类，定义了最简单的方法，装饰器类和被装饰类都要实现该接口。
 
　　**ConcreteComponent**，被装饰类，实现了Component。
 
　　**Decorator**，装饰器类，通过该类为ConcreteComponent动态添加额外的方法，实现了Component接口，并且该对象中持有一个Component的成员变量。
 
　　**ConcreteDecoratorA**，**ConcreteDecoratorB**，具体的装饰类，该类中的方法就是要为ConcreteComponent动态添加的方法。


## 2.案例实现
我们以生产一件衣服为例，生产一件衣服本身是个很简单的过程，一块布料裁剪好了之后做出衣服的样子就可以了，但是这样的衣服是卖不出去的，因为毫无美感，我们需要通过一些装饰来使衣服变得好看。但是时代在变化，人们的审美也在变化，装饰总是不断在变的，所以我们就要有一个灵活机动的模式来修改装饰。
![img](https://github.com/AmazingXiaomi/image/blob/master/445617DE-4F85-4890-9799-F6338F7B999C.png?raw=true)
  首先制作一件普通的衣服
````code
public interface Clothes {
    public void makeClose();
}
public class MakeClothes implements Clothes {
    @Override
    public void makeClose() {
        System.out.println("make a clothes");
    }
}
````
  话不多说，先来个衣服的最初成品，就是毫无美感的那种，那么如果现在要增加装饰，可以用一个类继承MakeClothes，然后增加里面makeClothes()方法，但是如果过几天装饰就变了，那么又要改动代码，而且如果装饰过多，这个类就显得很庞杂，不好维护，这个时候装饰器模式就来大显身手了。
````code
public class Decorator implements Clothes {
    private Clothes clothes;

    public Decorator(Clothes clothes) {
        this.clothes = clothes;
    }

    @Override
    public void makeClose() {
       clothes.makeClose();
    }
}
````
  这就是一个装饰器，它有一个构造函数，参数是一个衣服类，同时它重载了makeClothes()方法，以便它的子类对其进行修改。下面是两个子类，分别对衣服进行了绣花和镂空：
````code
public class Hollow extends Decorator {

    public Hollow(Clothes clothes) {
        super( clothes );
    }

    public void setHollow(){
        System.out.println("镂空了");
    }

    @Override
    public void makeClose() {
         super.makeClose();
         this.setHollow();
    }
}

public class Embroidery extends Decorator {

    public Embroidery(Clothes clothes) {
        super( clothes );
    }


    public void setColor(){
        System.out.println("五彩斑斓的黑！");
    }

    @Override
    public void makeClose() {
        super.makeClose();
        this.setColor();
    }
}
````

　　这两个子类的构造器都传入一个衣服模型，而且两个子类分别有各自的方法——绣花和镂空，但是他们均重写了makeClothes()方法，在制作衣服的过程中加入了绣花和镂空的操作，这样一来，我们只需要增删改这几个装饰器的子类，就可以完成各种不同的装饰，简洁明了，一目了然。下面测试一下：
````code
public class ClothesTest {
    public static void main(String[] args) {
        Clothes clothes= new MakeClothes();
        clothes=new Embroidery( clothes );
        clothes = new Hollow( clothes );
        clothes.makeClose();
        System.out.println("衣服做好了");
    }
}
````

## 3.总结
### 1.装饰器模式的优缺点
**优点**
1、装饰器模式与继承关系的目的都是要扩展对象的功能，但是装饰器模式可以提供比继承更多的灵活性。装饰器模式允许系统动态决定贴上一个需要的装饰，或者除掉一个不需要的装饰。继承关系是不同，继承关系是静态的，它在系统运行前就决定了

2、通过使用不同的具体装饰器以及这些装饰类的排列组合，设计师可以创造出很多不同的行为组合

**缺点**
由于使用装饰器模式，可以比使用继承关系需要较少数目的类。使用较少的类，当然使设计比较易于进行。但是另一方面，由于使用装饰器模式会产生比使用继承关系更多的对象，更多的对象会使得查错变得困难，特别是这些对象看上去都很像。

### 2.装饰器模式和适配器模式的区别

其实适配器模式也是一种包装（Wrapper）模式，它们看似都是起到包装一个类或对象的作用，但是它们使用的目的非常不一样：

1、适配器模式的意义是要将一个接口转变成另外一个接口，它的目的是通过改变接口来达到重复使用的目的

2、装饰器模式不要改变被装饰对象的接口，而是恰恰要保持原有的借口哦，但是增强原有接口的功能，或者改变元有对象的处理方法而提升性能

所以这两种设计模式的目的是不同的。