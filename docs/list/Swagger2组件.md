

### Swagger组件

`ApiBoot`内整合了swagger文档，方便查看接口API。



## 1. 添加依赖组件

```xml
 <dependency>
      <groupId>org.yxj.framework</groupId>
      <artifactId>spring-starter-swagger</artifactId>
 </dependency>
```



## 2. 配置参数



依赖Spring配置如下所示：

```yml
swagger:
    # 扫描包名
    base-package: me.zhyd
    title: Demo 大家好 我是测试
    description: demo
    version: 1.0
```

## 3. 使用

```java
此时，启动Spring Boot工程，在浏览器中访问：http://localhost:xxx/doc.html
```