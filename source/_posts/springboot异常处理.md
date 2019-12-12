---
title: Spring Boot异常处理
date: 2019-12-12 21:33:13
tags: [Java,Spring Boot]
---

异常处理分为两个部分，第一个部分是异常触发器的配置，第二个部分是触发结果的处理。

# Spring Boot触发器配置

通过继承默认的`ErrorController`来重写系统对于异常的处理

```java
@Controller
public class ErrorCustomConfig implements ErrorController {
    private static final String ERROR_PATH = "/error";

    @RequestMapping(value = ERROR_PATH)
    private String handleError(HttpServletResponse response) {
        int httpStatus = response.getStatus();
        switch (httpStatus) {
            case 400:
                return "/error/400";
            case 401:
                return "/error/401";
            case 403:
                return "/error/403";
            case 404:
                return "/error/404";
            case 500:
                return "/error/500";
            default:
                break;
        }
        return "/swagger-ui.html";
    }

    @Override
    public String getErrorPath() {
        return ERROR_PATH;
    }

}
```

以上是对于不同HTTP STATUS的处理。简言之，就是当发生不同的请求状态的时候转向的访问路径。

# Spring Boot触发器实现

配置好触发器之后，会根据不同的异常转向不同的请求地址，触发器的实现就是要去将这些地址的实际内容给补充好。

```java
@RestController
@RequestMapping(value = "/error/")
public class ErrorCustomController {


    @RequestMapping(value = "500")
    public Result meet500() {
        Result result = new Result();
        result.setCode(500);
        result.setMessage("出现错误");
        return result;
    }

    @RequestMapping(value = "404")
    public Result meet404() {
        Result result = new Result();
        result.setCode(404);
        result.setMessage("地址输错了");
        return result;
    }

    @RequestMapping(value = "403")
    public Result meet403() {
        Result result = new Result();
        result.setCode(403);
        result.setMessage("禁止外部访问");
        return result;
    }


    @RequestMapping(value = "401")
    public Result meet401() {
        Result result = new Result();
        result.setCode(401);
        result.setMessage("未获取访问权限，请重新登录");
        return result;
    }

    @RequestMapping(value = "400")
    public Result meet400() {
        Result result = new Result();
        result.setCode(400);
        result.setMessage("请求参数错误，请仔细核对参数");
        return result;
    }

}
```

这些状态的处理都是可以进行自定义的。