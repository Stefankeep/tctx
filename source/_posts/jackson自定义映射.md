---
title: Jackson自定义映射
date: 2020-02-19 13:58:39
tags: [Java,Spring Boot,VCM,Jackson]
---



`Jackson`在序列化反序列化`LocalDateTime`效果不如预期。共采用过两种方法进行修补，一种为配置序列器，一种为自定义`ObjectMapper`。



设定常量

```java
    /**
     * 日期格式化Formatter
     */
    String DATE_FORMAT = "yyyy/MM/dd";

    /**
     * 时间日期格式化Formatter
     */
    String DATETIME_FORMAT = "yyyy/MM/dd HH:mm:ss";

    /**
     * 时间格式化Formatter
     */
    String TIME_FORMAT = "HH:mm:ss";
```

日期使用`/`是为适配IOS端。

## 配置方式自定义映射

```java
@Configuration
public class JacksonLocalDateTimeFormatConfig {

    @Bean
    @Primary
    public Jackson2ObjectMapperBuilderCustomizer jackson2ObjectMapperBuilderCustomizer() {
        return builder -> builder.serializerByType(
                LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern(DATETIME_FORMAT)))
                .serializerByType(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern(DATE_FORMAT)))
                .serializerByType(LocalTime.class, new LocalTimeSerializer(DateTimeFormatter.ofPattern(TIME_FORMAT)))
                .deserializerByType(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern(DATETIME_FORMAT)))
                .deserializerByType(LocalDate.class, new LocalDateDeserializer(DateTimeFormatter.ofPattern(DATE_FORMAT)))
                .deserializerByType(LocalTime.class, new LocalTimeDeserializer(DateTimeFormatter.ofPattern(TIME_FORMAT)));
    }

}
```

配置的方式可以直接修改默认的序列化器，链式的方式也比较好看。

如果没有统一加密、缓存存储等需要在除了请求过程之外的地方也使用序列化器的操作，可以使用这种方式。



## 自定义`ObjectMapper`

```java
@Configuration
public class JacksonObjectMapper {

    @Bean
    @Primary
    public ObjectMapper serializingObjectMapper() {
        ObjectMapper objectMapper = new ObjectMapper();

        /*对于LocalDateTime的序列化和反序列化*/
        JavaTimeModule javaTimeModule = new JavaTimeModule();
        javaTimeModule.addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern(DATETIME_FORMAT)));
        javaTimeModule.addSerializer(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern(DATE_FORMAT)));
        javaTimeModule.addSerializer(LocalTime.class, new LocalTimeSerializer(DateTimeFormatter.ofPattern(TIME_FORMAT)));
        javaTimeModule.addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern(DATETIME_FORMAT)));
        javaTimeModule.addDeserializer(LocalDate.class, new LocalDateDeserializer(DateTimeFormatter.ofPattern(DATE_FORMAT)));
        javaTimeModule.addDeserializer(LocalTime.class, new LocalTimeDeserializer(DateTimeFormatter.ofPattern(TIME_FORMAT)));
        objectMapper.registerModule(javaTimeModule);
        objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        objectMapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
        objectMapper.setTimeZone(TimeZone.getTimeZone("Asia/Shanghai"));

        return objectMapper;
    }
}
```

自定义`ObjectMapper`更加灵活，可以在正常请求过程之外的地方也进行调用，以获得跟请求过程中的序列器相同的序列化效果。



比如可以直接采用自定义的映射器来序列化任意对象。

```java
Object body = new Object();
String res = new JacksonObjectMapper().serializingObjectMapper().writeValueAsString(body);
```



又比如直接采用自定义的映射器来序列化`Redis`的模板

```java
  	@Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
        template.setConnectionFactory(factory);
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        JacksonConfig jacksonConfig = new JacksonConfig();
        ObjectMapper om = new JacksonObjectMapper().serializingObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        template.setKeySerializer(stringRedisSerializer);
        template.setHashKeySerializer(stringRedisSerializer);
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
    }
```

