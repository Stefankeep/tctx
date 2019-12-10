---
title: spring boot多环境自定义配置文件中的信息
date: 2019-12-10 12:31:45
tags: [Java,Spring Boot]
---

# spring boot配置文件说明

`SpringBoot`的默认配置文件为`application.properties`,通常我们在使用的时候会通过修改后缀名的方式来将其变为`application.yml`.两者在本质上面没有任何的区别,区别只在于书写配置文件的格式.

更加推荐使用`yml`的方式,结构更加的清晰,对列表配置的表达也更易于理解.

在实际开发中同一个项目会需要运行在多个环境中,比如开发环境\测试环境\预发布环境\正式环境等诸多自定义环境中.

`SpringBoot`提供了简便的方式,通过激活不同的`profile`就可以了.

```powershell
application.yml #主配置文件
application-dev.yml #开发环境配置文件
application-test.yml #测试环境配置文件
application-pre.yml #预发布环境配置文件
application-prod.yml #生产环境配置文件
```

主配置文件中公共的配置,如`servlet以及server这样的配置`.然后通过激活不同的`profile`来加入不同的配置文件中内容,比如不同的配置文件代表在不同的环境中使用不同的数据源\日志配置等.

启用不同配置文件的方式为在`application.yml`中

```yaml
spring:
  profiles:
    active: dev #启用开发环境的配置
```

简单理解就是,在如上情况下,当前应用的配置是`application.yml`以及`application-dev.yml`的和.

# spring boot获取配置文件中信息

有一些时候,我们需要在不同的环境中采用不同的配置信息,那么我们无法将其写入程序配置中,就可以借用`spring boot`的多环境配置文件的方式来储存这些配置信息.

而要获取这些信息有很多中方式,下面介绍最普遍也最不实用的三种

```java
/**
 * yml配置文件中如
 *  custom:
 *		key: 123456
 */


/**
 * 第一种
 * 这样可以获取到,但有个问题,就是哪里要用就要写在哪里,而且不是静态属性,不能够进入静态方法
 */

@Value("${custom.key}")
private String customKey;


/**
 * 第二种
 * 这种方式也可以获取到,需要每次将这个注册的组件进行注入.问题也同样明显,不能够进行静态方法.
 * 之所以强调要能够满足静态的方法,是因为一般这种自定义的配置都是要进入静态方法,然后在别处进行调用.
 * 而这种方法,其他地方要进行调用也只能将同样的组件进行注入才能进行使用.也即是非应用单例.
 */
@Component
@ConfigurationProperties(prefix="custom")
public class CustomProp {
    
    private String key;
    
    public String getKey(){
        this.key;
    }
    public void setKey(String key){
        this.key = key;
    }
}

/**
 * 第三种
 * 这种方式就是取得当前的应用环境然后以读取yml文件的方式来获取配置项.
 */
public class CustomProp {
    private Environment env;

	public CustomProp(Environment env){
        this.env = env;
    }
    
    private void getProp(){
        String key = environment.getProperty("custom.key");
    }
}

```

而要满足我们的需要**在不同的环境中获得相应的配置,能够像静态属性一样随处使用.**

就需要一些融合的方式

```java
@Repository //springboot2.x以下也可以使用@Component只要能注册是javaBean就行
public class ConstantsPropMul {

    public static String KEY;

    @Value("${custom.key}")
    private String key;


    @PostConstruct
    public void trans() {
        KEY = this.key;
    }
}

```

上面的方式仍然通过`@Value`来获取当前环境的配置项.

然后定义一个静态属性`KEY`,通过`@PostConstruct`注释来让`trans`在依赖关系注入完了之后运行一遍.

而**trans会让获取到的key值赋给静态属性KEY**.

这样的单例静态属性一样可以随处调用了.基本等同于

```java
public interface ConstantsPropSingle {
	private String KEY;
}
```

