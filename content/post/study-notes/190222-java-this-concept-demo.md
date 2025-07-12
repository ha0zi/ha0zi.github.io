+++
title = 'Java中关于"this"的概念测试'
date = 2019-02-22T16:52:00+08:00
draft = true
categories = ["学习笔记"]
tags = ["JAVA"]
+++
一直以来我对`this`关键字的理解都是当前类,然后今天在查看一个教程的时候因为这个观念,我因为这个观念产生了不一样的观点,然后被人告知`this`代表的是'执行者',对此我使用代码进行demo 测试;

> 直接上代码
```java
public class SuperClass {
    public void test(){
        System.out.println("Super");
        System.out.println(this.getClass().toString());
    }

    public void ceshi(){
        System.out.println(this.getClass().toString());
    }
}

class TestClass extends SuperClass{

    @Override
    public void test() {
        System.out.println(this.getClass().toString());
        super.test();
        super.ceshi();
    }

    public static void main(String[] args) {
        TestClass testClass = new TestClass();
        testClass.test();
    }
}
```
<!-- more -->
可以看出`TestClass`继承`SuperClass`并且重写了`test()`方法,那么我们运行`main()`方法,查询一下结果
```
class TestClass
Super
class TestClass
class TestClass
```
我们可以看出来所有的`this`在控制台中打印的结果是我们的子类,因为继承的关系,在父类的`ceshi()`方法中会被继承到子类中,所以这里打印的`super.ceshi()`方法打印的结果是`TestClass`我认为很合理,但是在子类重写的`test()`方法中,调用父类的被重写的`test()`方法确仍然打印出`TestClass`,那么就可以判断处我之前的观念确实是存在问题,那么在将他人讲解的*执行者*概念套上在理解,确实可以解释的通的;那么至少说明我之前理解的概念是不完全正确的

由此我们可以暂时性的将`this`的概念理解为执行者,直至出现反驳以及更好的说明
