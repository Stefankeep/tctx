---
title: Spring Boot启动过程
date: 2020-02-19 13:41:50
tags: [Java,Spring Boot,VCM]
---

##  原生注解`@PostConstruct`

Java-SDK中自带的启动注解：当class进行初始化的时候会调用由`@PostConstruct`注解的函数。

因此可以用作读取静态文件中的常量、健康检测等。**但需要注意的是，这个注释是在class初始化时会进行调用，不是Spring Boot 应用启动之后进行调用**。



## 实现Spring boot应用启动接口

Spring boot提供了`ApplicationRunner`作为一个可实现的接口，来完成当应用启动之后需要立即进行操作的。

```java
@Repository
public class ApplicationInit implements ApplicationRunner {    
    @Override    
    public void run(ApplicationArguments args) {        
        System.out.println("Hello World ~");   
    }
}
```

上述Class实现了当应用启动之后会打印一句Hello World ~。

