---
layout: post
title: 注解
description: "关于Java的注解"

tags: ["JAVA"]
comments: true
---

因为Android很多Demo都用到了注解,所以不会注解,很多代码都看得不是很懂.如广为流行的**ButterKnife**, **Dagger2**, **AndroidAnnotations** 等等,所以没有一点注解的知识,看起代码来确实有些不便.所以才有了这一篇笔记.

***

**什么是注解:**  
注解是 Java 5 的一个新特性。注解是插入你代码中的一种注释或者说是一种元数据（meta data）。这些注解信息可以在编译期使用预编译工具进行处理（pre-compiler tools），也可以在运行期使用 Java 反射机制进行处理。  
*注意,这里可以在预编译时进行,也可以在运行中进行,如果在运行中进行,就需要考虑注解导致的效率问题!!*

**注解类型:**  
1. 类注解
2. 方法注解
3. 参数注解
4. 变量注解

**元注解:**  
1. @Documented —— 指明拥有这个注解的元素可以被javadoc此类的工具文档化。这种类型应该用于注解那些影响客户使用带注释的元素声明的类型。如果一种声明使用Documented进行注解，这种类型的注解被作为被标注的程序成员的公共API
2. @Target——指明该类型的注解可以注解的程序元素的范围。该元注解的取值可以为TYPE,METHOD,CONSTRUCTOR,FIELD等。如果Target元注解没有出现，那么定义的注解可以应用于程序的任何元素。
3. @Inherited——指明该注解类型被自动继承。如果用户在当前类中查询这个元注解类型并且当前类的声明中不包含这个元注解类型，那么也将自动查询当前类的父类是否存在Inherited元注解，这个动作将被重复执行知道这个标注类型被找到，或者是查询到顶层的父类。
4. @Retention——指明了该Annotation被保留的时间长短。RetentionPolicy取值为SOURCE,CLASS,RUNTIME。

当然还可自定义注解,方法也很简单,就不在这里做介绍了.

注解通过Java的反射机制,来获取annotation中的信息,根据这些信息做出不同的操作,通过配套的工具,对注解进行访问和处理,例如Javadoc对命令对@Document注解进行解析并生成文档.

**使用的地方:**  
1. 向编译器提供信息,例如@Deprecated,来想编译器提醒该方法已经过时,
2. 文档,通过一些工具,对注解进行解析然后生成文档,例如@Documented和javadoc命令的使用.
3. 代码生成,注解可以使用代码中展示的元数据信息来自动生成代码或者XML文件..
4. 运行时处理,在运行时坚持注解可以做出不同的目的,像单元测试(JUnit)...

在android中,为了实现findViewById的省略,可以利用第3,4种方法来实现.

[参考](http://ifeve.com/java-annotations-tutorial/)
