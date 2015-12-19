---
layout: post
title: 关于Java中hashCode和equal方法
description: "java"

tags: ["Java"]
comments: true
---

前一段时间,项目中有个User的类,在里面实现了equal的方法,来对比两个User对象是否相同.equal方法还是很好理解的,通过里面的每一个字段进行一一比对[其实也不是每一个,应该是你需要比对的字段],来确定这两个对象是否同相同.Object中原有的equal对比的是两个对象的内存地址是否相同...

但是hashCode究竟是什么作用,虽然通过名字知道得出来的是一个散列值.可是具体是干嘛用的呢?

通过查找资料,以下例子能很好地说明一个问题.

```
public class User {

    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        User user = (User) o;

        if (age != user.age) return false;
        return name.equals(user.name);

    }

//    @Override
//    public int hashCode() {
//        int result = name.hashCode();
//        result = 31 * result + age;
//        return result;
//    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

}
```
这是User实体.其中hashCode被我注释了.

```
public static void main(String[] args){
        HashSet<User> set = new HashSet<>();

        User user1 = new User("kevin", 26);
        User user2 = new User("kevin", 26);

        set.add(user1);
        set.add(user2);

        System.out.print("count=" + set.size());

    }
```

通过上面的main方法,不实现hashCode的时候,输出的size为2,实现hashCode的时候,输出的size为1.

从这里可以了解,hashCode的功能类似返回一个地址,但是不是内存存放的地址(只是类似而已),通过这个方法来确认是否同一个对象...

官方文档建议实现equal方法的时候,也需要实现hashCode方法.

至于hashCode应该如何实现,其实可以通过IDE直接生产便可以.eclipse和Android studio都有方法.