---
title: 'Java8 Lambda 杂记'
date: 2019-03-11T19:00:00+08:00
draft: false
categories: ["学习笔记"]
tags: ["JAVA"]
---
*ps: 目前阅读学习《Java 8 函数式编程》,本博文是阅读笔记,有意者可自行通过互联网进行资源获取,或者通过[京东](https://www.jd.com)等购物网站进行正版购买;*

#### 概念

听说了很多函数式编程的 牛A、牛B、牛C 之处,说实话并不清楚[函数式编程](https://baike.baidu.com/item/%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BC%96%E7%A8%8B/4035031?fr=aladdin)的本质是什么;百度看了百度百科也是云里雾里,引用一下书中的两处介绍

> " 但其核心是:在思考问题时,使用不可变值和函数,函数对一个值进行处理,映射成另外一个值. "

> " 和传入一个实现某接口的对象不同,我们传入了一段代码块---一个没有名字的函数."
>
>> `button.addActionListener(event -> System.out.println("button clicked"));`
>>

在引用正确的前提下,在我看来重点是以下三点:

- 不可变值和函数
- 函数对一个值进行处理,映射成另外一个值
- 传入了一段代码块
<!-- more -->

#### Lambda 表达式的书写形式

- 空参传入
  - `Runnable noArguments = () -> System.out.println("Hello World");`
- 参数传入
  - `ActionListener oneArgument = event -> System.out.println("button clicked");`
- 代码块传入
  - ```
    Runnable multiStatement = () -> {
    System.out.print("Hello");
    System.out.println(" World");
    };
    ```
- 创建函数传入
  - `BinaryOperator<Long> add = (x, y) -> x + y;`
- 创建函数传入, 声明参数类型
  - `BinaryOperator<Long> add = (Long x, Long y) -> x + y;`

#### Java 8 中重要的函数接口

| 接口 | 参数 | 返回类型 | 示例 |
|:-- |:-- |:-- |:-- |
| Predicate<T> | T | boolean | 唱片是否发行 |
| Consumer<T> | T | void | 输出一个值 |
| Function<T, R> | T | R | 获得 Artist 对象的名字 |
| Supplier<T> | None | T | 工厂方法 |
| UnaryOperator<T> | T | T | 逻辑非(!) |
| BinaryOperator<T> | (T, T) | T | 求两个数的乘积(*) |

#### 思考

1. 那么是不是便于理解的认为函数式编程的本质是传入可执行**代码块**?
2. 那么和匿名内部类有什么本质的区别吗?
3. 相对于匿名内部类的优势是什么?

#### 思考验证

1. 那么是不是便于理解的认为函数式编程的本质是传入可执行**代码块**?

通过一下 Demo 我们可以发现,创建对象之后我们无法直接运行函数对象,仍然需要调用方法去引用才会执行,那么可以说明创建的对象中并没有直接执行函数方法

```java
public class Demo {
    /**该变量代表的不是具体的数值,而是表示'{x + y;}'这个代码块*/
    BinaryOperator<Integer> add = (x, y) -> x + y;
    /**按钮*/
    private JButton button = new JButton();
    /***/
    private ActionListener actionListener;

    @Test
    public void test(){
        System.out.println(add.apply(3, 4));
        System.out.println(add.apply(4, 6));

        final Integer a = 1;
//        a = 2;
        Runnable run = new Runnable(){
            @Override
            public void run() {
                System.out.println(a);
            }
        };

    }

    /**
     * 这里可以看出使用 lambda 表达式进行赋值,进行打印的都不是具体的数值,
     *  ~调用对应的方法却得到我们最终想要的打印,那么是否说明 Lambda 表达式
     *  ~给予其赋值的并不是具体的值,而是代码块?
     */
    @Test
    public void test1(){
        String name = "This's Demo";
//        actionListener = (event) -> System.out.println(name);
//        System.out.println(actionListener);
//        actionListener.actionPerformed(null);
//
//        button.addActionListener(event -> System.out.println(name));
//        System.out.println(button);
//        button.getActionListeners()[0].actionPerformed(null);
//        System.out.println(name);
        Consumer c = (event) -> System.out.println(name);
        Consumer o = (event) -> System.out.println("Demo");
        //在原来基础上进行添加 返回一个新的Consumer
        Consumer consumer = c.andThen(o);
//        c.accept(2);
        consumer.accept(1);

        new Thread(() -> System.out.println("Hello World!")).start();
    }
}
```

> 我们查看 Consumer 的源码注释

```java
package java.util.function;

import java.util.Objects;

/**
 * Represents an operation that accepts a single input argument and returns no
 * result. Unlike most other functional interfaces, {@code Consumer} is expected
 * to operate via side-effects.
 *
 * <p>This is a <a href="package-summary.html">functional interface</a>
 * whose functional method is {@link #accept(Object)}.
 *
 * @param <T> the type of the input to the operation
 *
 * @since 1.8
 */
@FunctionalInterface
public interface Consumer<T> {

    /**
     * Performs this operation on the given argument.
     *
     * @param t the input argument
     */
    void accept(T t);

    /**
     * Returns a composed {@code Consumer} that performs, in sequence, this
     * operation followed by the {@code after} operation. If performing either
     * operation throws an exception, it is relayed to the caller of the
     * composed operation.  If performing this operation throws an exception,
     * the {@code after} operation will not be performed.
     *
     * @param after the operation to perform after this operation
     * @return a composed {@code Consumer} that performs in sequence this
     * operation followed by the {@code after} operation
     * @throws NullPointerException if {@code after} is null
     */
    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

`Performs this operation on the given argument.`翻译成中文就是 *"对给定的论证者执行此操作"*,既然需要调用此方法才能执行函数;至少我们可以确定函数式编程中存储的并不是运算结果,具体的存储内容我们现在我并不知道如何去查看,但是可以阶段性的理解为将代码块已对象的形式表达,就是函数式编程;

就 `Consumer<T>`对象来说,它拥有两个方法 *void accept(T t)*用以执行代码; *Consumer<T> andThen(Consumer<? super T> after)*创建一个函数对象,这个新的函数是在已有函数后继续执行其他函数;(可以理解为{a = 1;} + {b = 1;} = {a=1;b=1;})

2. 那么和匿名内部类有什么本质的区别吗?
   1. 匿名内部类代表的是数据对象,而 lambda 表示的是函数对象.
   2. 使用 lambda 表达式也可以创建匿名内部类.
3. 相对于匿名内部类的优势是什么?
   1. 书写更简洁,代码格式美
   2. 不仅仅适用于 Java 这个语言
   3. 我认为更灵活,例如 `Consumer` 的 *andThen()* 方法
