---
layout: post
title: Design Patterns Bridge
description: “桥接模式"

tags: ["Design Patterns"]
comments: true
---

###Bridge Pattern
桥接模式做得事情就是把抽象定义的部分与实现内容的部分分离。

![Alt text]({{ site.url }}/images/dp/bridge_chart1.png)
为了加多一个IconWindow的需要加一个Xwindow下的IconWindow，在PMIconWindow下的IconWindow。

![Alt text]({{ site.url }}/images/dp/bridge_chart2.png)
所以可以把实现抽离出来，通过接口调用对应的实现。

[桥接的结构图]
![Alt text]({{ site.url }}/images/dp/bridge_chart3.png)

[桥接的代码实现]

```java
class Abstraction
{
  // Fields
  protected Implementor implementor;

  // Properties
  public Implementor Implementor
  {
    set{ implementor = value; }
  }

  // Methods
  virtual public void Operation()
  {
    implementor.Operation();
  }
}
```

```java
// "Implementor"
abstract class Implementor
{
  // Methods
  abstract public void Operation();
}

// "RefinedAbstraction"
class RefinedAbstraction : Abstraction
{
  // Methods
  override public void Operation()
  {
    implementor.Operation();
  }
}
```

```java
// "ConcreteImplementorA"
class ConcreteImplementorA : Implementor
{
  // Methods
  override public void Operation()
  {
    Console.WriteLine("ConcreteImplementorA Operation");
  }
}

// "ConcreteImplementorB"
class ConcreteImplementorB : Implementor
{
  // Methods
  override public void Operation()
  {
    Console.WriteLine("ConcreteImplementorB Operation");
  }
}
```

```java
/// <summary>
/// Client test
/// </summary>
public class Client
{
  public static void Main( string[] args )
  {
    Abstraction abstraction = new RefinedAbstraction();

    // Set implementation and call
    abstraction.Implementor = new ConcreteImplementorA();
    abstraction.Operation();

    // Change implemention and call
    abstraction.Implementor = new ConcreteImplementorB();
    abstraction.Operation();
  }
}
```