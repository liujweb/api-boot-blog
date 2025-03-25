

### 分布式ID组件

`ApiBoot`内整合了分布式高效的ID生成方式，基于Twitter的Snowflake算法实现分布式高效有序ID生产黑科技。



## 1. 添加依赖组件

```xml
 <dependency>
      <groupId>org.bz.framework</groupId>
      <artifactId>spring-starter-sequence</artifactId>
 </dependency>
```



## 2. 配置参数



依赖Spring-Redis配置如下所示：

```yml
spring:
    redis:
        database: 0
        host: 127.0.0.1
        jedis:
            pool:
                max-active: 8
                max-idle: 8
                max-wait: -1
                min-idle: 3
   	
```

## 4. 使用

```java
// 获取下一个Long类型的ID
long id = YitIdHelper.getIdGenInstance().newLong();
