---
title: java设计模式-责任链模式 
date: 2019-07-27 16:03:32
description: '责任链模式（Chain of Responsibility），行为型设计模式之一。'
tags: java设计模式
---

# 责任链模式

## 1.简述
  **责任链模式**（Chain of Responsibility），行为型设计模式之一。什么是责任链呢？这个链的形式更像是数据结构中的单链表，链中的每个节点都有自己的职责，同时也持有下一个节点的引用，
  属于自己职责范围内的请求就自行处理，并完成请求的处理；而不属于的职责就传递给下一个节点。每个节点都是如此循环，直至请求被处理或者已经没有处理节点。
  
   这种设计模式是为了避免请求的发送者和接收者之间的耦合关系，而责任链就是中间的请求处理者，其中可能包括多个有可能处理请求的对象，并将这些对象炼成一条链。这样也使得请求发送者无需关心请求的处理细节和请求的传递。
   
   ![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/595349-eea9ef31f92adee2.webp)
   - Client：客户端，请求的发起者
   - Handler：抽象处理者，声明一个请求方法，并在其中保持一个对下一个处理节点Handler对象的引用
   - ConcreteHandler：具体处理角色，对请求进行处理；如果不能处理则将请求转发给下一个节点对象处理
   
   
## 2.案例实现

### 场景介绍
  以公司正常请假为例，1天以内的假需要客户端部门主管签字，3天以内（不包含3天）需要技术部门主管签字，3天及以上就需要找CEO签字。
  
  首先是假条的类，包含姓名、请假原因和请假时间，使用final声明属性只是为了避免外部修改属性而已。
  ```code
  /**
   * @ClassName: LeaveNote
   * @User: xiaomi
   * @Description: //请假条
   * @Time: 2019/6/26 0026 下午 1:58
   * @email: a1205938863@gmail.com
   */
  
  public class LeaveNote {
      private String name;
      private String reason;
      private int leaveDays;
  
      public LeaveNote(String name, String reason, int leaveDays) {
          this.name = name;
          this.reason = reason;
          this.leaveDays = leaveDays;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getReason() {
          return reason;
      }
  
      public void setReason(String reason) {
          this.reason = reason;
      }
  
      public int getLeaveDays() {
          return leaveDays;
      }
  
      public void setLeaveDays(int leaveDays) {
          this.leaveDays = leaveDays;
      }
  }
  
  ```
  
  接下来就是比较重要的Handler类，代码比较简单，下一个Handler的对象引用，再就是handle()和setNextHandler()方法，不多解释了，和之前的UML图中的的结构是一样的.
  ```code 
  /**
   * @ClassName: Handler
   * @User: xiaomi
   * @Description: //TODO
   * @Time: 2019/6/26 0026 下午 1:59
   * @email: a1205938863@gmail.com
   */
  public  interface Handler {
       Optional<String> hand(LeaveNote note);
       void setNextHandler(Handler nextHandler);
       Boolean checkLeave(int leave);
  }
  ```
  
  然后就是他的实现类，这也是个抽象类，这个类里有个hand()方法，为了统一管理责任链的链式流程。
  ```code 
  /**
   * @ClassName: AbsractHandler
   * @User: xiaomi
   * @Description: //TODO
   * @Time: 2019/6/26 0026 下午 2:02
   * @email: a1205938863@gmail.com
   */
  public abstract class AbsractHandler implements Handler {
      private Handler handler;
  
  
      @Override
      public Optional<String> hand(LeaveNote note) {
          if (checkLeave( note.getLeaveDays() )){
             return doHand( note );
          }else {
              setNextHandler( handler );
              return handler.hand( note );
          }
      }
  
      @Override
      public void setNextHandler(Handler nextHandler) {
          this.handler=nextHandler;
      }
  
      protected abstract Optional<String> doHand(LeaveNote note);

  ```
  和抽象类的具体实现类，这些就是责任链上的每一个点
  ```code 
  /**
   * @ClassName: Ceo
   * @User: xiaomi
   * @Description: //TODO
   * @Time: 2019/6/26 0026 下午 2:12
   * @email: a1205938863@gmail.com
   */
  public class Ceo extends AbsractHandler {
      @Override
      protected Optional<String> doHand(LeaveNote note) {
          String s="CEO同意！";
          return Optional.ofNullable( s );
      }
  
      @Override
      public Boolean checkLeave(int leave) {
          return leave>3;
      }
  
      @Override
      public void setNextHandler(Handler nextHandler) {
          super.setNextHandler( nextHandler );
      }
  }
  public class ClientLeader extends AbsractHandler {
      @Override
      protected Optional<String> doHand(LeaveNote note) {
          String s="上级同意！";
          return Optional.ofNullable( s );
      }
  
      @Override
      public Boolean checkLeave(int leave) {
          return leave<=1;
      }
  
      @Override
      public void setNextHandler(Handler nextHandler) {
          super.setNextHandler( nextHandler );
      }
  }
  public class TechnologyLeader extends AbsractHandler {
      @Override
      protected Optional<String> doHand(LeaveNote note) {
          String s="技术领导同意！";
          return Optional.ofNullable( s );
      }
  
      @Override
      public Boolean checkLeave(int leave) {
          return leave <= 3;
      }
  
      @Override
      public void setNextHandler(Handler nextHandler) {
          super.setNextHandler( nextHandler );
      }
  }
  ```
  测试一下
  ```code 
  
/**
 * @ClassName: ClientLeave
 * @User: xiaomi
 * @Description: //责任链模式
 * @Time: 2019/6/26 0026 下午 2:32
 * @email: a1205938863@gmail.com
 */
public class ClientLeave {
    public static void main(String[] args) {
        LeaveNote leaveNote7=new LeaveNote("xiaomi","三年没回去，媳妇怀孕了",7);
        LeaveNote leaveNote3=new LeaveNote("xiaomi","三年没回去，媳妇怀孕了",3);
        LeaveNote leaveNote1=new LeaveNote("xiaomi","三年没回去，媳妇怀孕了",1);

        Handler clientLeader=new ClientLeader();
        Handler technologyLeader=new TechnologyLeader();
        Handler ceo=new Ceo();
        clientLeader.setNextHandler(technologyLeader  );
        technologyLeader.setNextHandler(ceo  );
        System.out.println(clientLeader.hand(leaveNote7  ).get());
        System.out.println(clientLeader.hand(leaveNote3  ).get());
        System.out.println(clientLeader.hand(leaveNote1  ).get());
    }

}
  ```
  
![img](https://raw.githubusercontent.com/AmazingXiaomi/image/master/Responsibilit-diagrams.jpg)



## 3.总结

责任链的优点就在于请求者和接受者松散耦合，以及能动态组合职责。
例子中可以看出，请求者不知道接受者是谁，也不知道具体的处理过程，只需要发出请求就行了。而对于每个职责对象来说，也不关心请求者和其他的职责对象（虽然持有下一个职责对象的引用），只负责处理自己职责的部分，其他的就交给其他的职责对象去处理。
动态组合职责则是利用对于职责的拆分，可以灵活的组合形成责任链，从而可以灵活的分配职责，也可以灵活的实现职责对象。
其实会发现，为了处理一个请求，我们可能会创建很多个职责对象，但是最后实际执行的最多只有一个职责对象（甚至没有），为了兼容更多职责就需要更多地职责对象。可见细化职责的同时，我们也在不断的增加对象，这不是一个好现象；而且在职责对象中，可能需要提供默认处理，不然请求很可能不会被责任链中的任何一个职责对象处理。
 ````bash 
  分离职责，动态组合
 ````

转载
作者：MrTrying
链接：https://www.jianshu.com/p/b1ddd8a868e0
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。    