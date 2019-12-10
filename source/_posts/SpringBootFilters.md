---
title: Spring Boot拦截器
date: 2019-12-10 13:16:28
tags: [Java,Spring Boot]
---

# Spring Boot的拦截器 

`Spring Boot`的拦截器实际上是对`HttpServlet`的生命周期进行拦截

```java
@Component
@Slf4j
public class CustomFilter implements HandlerInterceptor {
    /**
     * 请求开始前
     *
     * @param httpServletRequest  request servlet请求
     * @param httpServletResponse response serlet返回
     * @param o                   o 自定义的参数
     * @return boolean 是否进行拦截,true则继续进行,false则拒接请求
     */
    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o){
        log.info("请求的IP是" + getRealIP(httpServletRequest));
        httpServletRequest.setAttribute("start", LocalDateTime.now());
        return true;
    }

     /**
     * 请求已经执行了但是还未渲染
     *
     * @param httpServletRequest  request servlet请求
     * @param httpServletResponse response serlet返回
     * @param o                   o 自定义的参数
     * @param ModelAndView modelAndView 渲染的view
     */
    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView){
    }

     /**
     * 请求结束后
     *
     * @param httpServletRequest  request servlet请求
     * @param httpServletResponse response serlet返回
     * @param o                   o 自定义的参数
     * @throws Exception e 抛出异常
     */
    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e){
        Duration duration = Duration.between(LocalDateTime.now(), (LocalDateTime) httpServletRequest.getAttribute("start"));
        log.info("请求" + httpServletRequest.getRequestURI() + "共耗时" + duration.toMillis() + "ms");
    }
}
```

简单来说就是对`HandlerInterceptor`接口进行实现,来在`HttpServlet`的各个生命周期触发相应的操作.

以上是通过拦截器实现计算一个请求所花费的时间,

# Spring Boot的拦截器配置

在编辑好拦截器后,需要对拦截器进行配置.

```java
@Configuration
public class WebAppConfig implements WebMvcConfigurer {

    /**
     * 请求上下文拦截器
     *
     * @param registry 拦截器注册
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new CustomFilter()).addPathPatterns("/**")
                .excludePathPatterns("/error/**");
    }

}
```

配置拦截器就是对`WebMvcConfigurer`进行实现.可以配置多个拦截器来满足不同的业务,也能够对不同的拦截器来配置其生效的范围.