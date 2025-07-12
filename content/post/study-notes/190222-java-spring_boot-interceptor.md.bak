+++
title = 'SpringBoot 拦截器学习'
date = 2019-02-22T14:11:00+08:00
draft = true
categories = ["学习笔记"]
tags = ["JAVA","SpringBoot"]
+++
阅读《Java EE 开发的颠覆者 Spring Boot 实战》时，在 第二部分->第四章 SpringMVC 基础 中介绍到拦截器的使用，这里介绍拦截器的配置可让普通Bean 实现`HandlerInterceptor` 接口或者继承`HandlerInterceptorAdapter` 类来实现自定义拦截器.

因为抽象类`HandlerInterceptorAdapter` 会多提供一个`afterConcurrentHandlingStarted` 方法,所以我们先根据继承`HandlerInterceptorAdapter` 的自定义拦截器类进行深入研究.这里我们先将相关的源码看一看.
<!-- more -->
> HandlerInterceptorAdapter

```java
package org.springframework.web.servlet.handler;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.lang.Nullable;
import org.springframework.web.servlet.AsyncHandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

public abstract class HandlerInterceptorAdapter implements AsyncHandlerInterceptor {
    public HandlerInterceptorAdapter() {
    }

    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return true;
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
    }

    public void afterConcurrentHandlingStarted(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    }
}
```
> AsyncHandlerInterceptor

```java
package org.springframework.web.servlet;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface AsyncHandlerInterceptor extends HandlerInterceptor {
    default void afterConcurrentHandlingStarted(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    }
}
```

通过以上源码我们可以看出继承`HandlerInterceptorAdapter` 实际也是通过实现接口`HandlerInterceptor` 来达到自定义拦截器的目的,他们之间的区别在于通过接口`AsyncHandlerInterceptor` 进行了功能增强,从而进一步查看异步请求拦截的效果

---
### 具体自定义拦截器代码
> 配置自定义拦截器到Spring

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @author hz
 * @date 19-2-6 16:16
 */
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Autowired
    private TestHandlerInterceptorAdapter testHandlerInterceptorAdapter;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(testHandlerInterceptorAdapter).addPathPatterns("/**/*");
    }
}
```
> 继承HandlerInterceptorAdapter 的实例

```java
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @author hz
 * @date 19-2-6 10:48
 */
public class TestHandlerInterceptorAdapter extends HandlerInterceptorAdapter {
    /**
     * 在业务处理器处理请求之前被调用。预处理，可以进行编码、安全控制等处理；
     * @param request
     * @param response
     * @param handler
     * @return
     * @throws Exception
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return super.preHandle(request, response, handler);
    }

    /**
     * 在业务处理器处理请求执行完成后，生成视图之前执行。后处理（调用了Service并返回ModelAndView，但未进行页面渲染），有机会修改ModelAndView；
     * @param request
     * @param response
     * @param handler
     * @param modelAndView
     * @throws Exception
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        super.postHandle(request, response, handler, modelAndView);
    }

    /**
     * 在DispatcherServlet完全处理完请求后被调用，可用于清理资源等。返回处理（已经渲染了页面），可以根据ex是否为null判断是否发生了异常，进行日志记录；
     * @param request
     * @param response
     * @param handler
     * @param ex
     * @throws Exception
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        super.afterCompletion(request, response, handler, ex);
    }

    /**
     * 当Controller中有异步请求方法的时候会触发该方法, 异步请求先支持preHandle、然后执行afterConcurrentHandlingStarted, 异步线程完成之后执行会再执行preHandle、postHandle、afterCompletion
     * @param request
     * @param response
     * @param handler
     * @throws Exception
     */
    @Override
    public void afterConcurrentHandlingStarted(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        super.afterConcurrentHandlingStarted(request, response, handler);
    }
}
```
> 测试Controller

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author hz
 * @date 19-2-6 10:29
 */
@RestController
@RequestMapping
public class TestController {

    @GetMapping("/test")
    public String test(){
        return "Hello World!";
    }
}
```
为了便于测试,这里我们将`TestHandlerInterceptorAdapter` 的方法进行修改以达到测试的效果
```java
public class TestHandlerInterceptorAdapter extends HandlerInterceptorAdapter {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           super.preHandle(request, response, handler);
           System.out.println("TestHandlerInterceptorAdapter::preHandle");
           String uri = request.getRequestURI();
           System.out.println("当前访问uri : " + uri);

           if("/test".equals(uri)){
               return true;
           }
           if("/error".equals(uri)){
               return true;
           }
           System.out.println("拦截器执行拦截");
           return false;
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        super.postHandle(request, response, handler, modelAndView);
        System.out.println("TestHandlerInterceptorAdapter::postHandle");
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        super.afterCompletion(request, response, handler, ex);
        System.out.println("TestHandlerInterceptorAdapter::afterCompletion");
    }

    public void afterConcurrentHandlingStarted(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        super.afterConcurrentHandlingStarted(request, response, handler);
        System.out.println("TestHandlerInterceptorAdapter::afterConcurrentHandlingStarted");
    }
}
```
我们先进行访问`localhost:8080` 以及未注册路由的请求uri 发现它的实际访问是`/localhost:8080/error` 并且发现该路由已经被注册(`org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration.WhitelabelErrorViewConfiguration#defaultErrorView`),不过这是后话,有兴趣的朋友可以研究一下

下面我依次访问 `localhost:8080/test` `localhost:8080` `localhost:8080/ceshi`

```
\---------------------------
TestHandlerInterceptorAdapter::preHandle
当前访问uri : /test
TestController:test
TestHandlerInterceptorAdapter::postHandle
TestHandlerInterceptorAdapter::afterCompletion
---------------------------
TestHandlerInterceptorAdapter::preHandle
当前访问uri : /error
TestHandlerInterceptorAdapter::postHandle
TestHandlerInterceptorAdapter::afterCompletion
---------------------------
TestHandlerInterceptorAdapter::preHandle
当前访问uri : /ceshi
拦截器执行拦截
```

我们可以清楚的看到它们运行了那些方法以及运行这些方法的顺序,以此来根据自己的需求进行拦截器的编写.
具体代码[这里](https://github.com/ha0zi/demo/tree/master/interceptor)
