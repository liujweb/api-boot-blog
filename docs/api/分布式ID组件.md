

### 分布式ID组件

`ApiBoot`内整合了分布式高效的ID生成方式，基于Twitter的Snowflake算法实现分布式高效有序ID生产黑科技。



## 1. 添加依赖组件

```xml
 <dependency>
      <groupId>org.yxj.framework</groupId>
      <artifactId>spring-starter-sequence</artifactId>
 </dependency>
```



## 2. 特性

1. 支持自定义允许时间回拨的范围
2. 解决跨毫秒起始值每次为0开始的情况（避免末尾必定为偶数，而不便于取余使用问题）
3. 解决高并发场景中获取时间戳性能问题
4. 支撑根据IP末尾数据作为workerId
5. 时间回拨方案思考：1024个节点中分配10个点作为时间回拨序号（连续10次时间回拨的概率较小）

## 3. 配置参数

如下所示：

```yml
api:
    boot:
        sequence:
            # 工作机器的编号，默认值为1，取值的范围：0 ~ 255
            worker-id: 1
            # 数据中心的编号，默认值为：1，取值的范围：0 ~ 3
            data-center-id: 1
            # 解决高并发下获取时间戳的性能问题
            clock: true
```

## 4. 使用

```java
/**
  * 注入ApiBoot提供的分布式ID生成类
  * <p>
  * 调用{@link ApiBootSequenceContext#nextId()}、{@link ApiBootSequenceContext#nextStringId()}方法可以直接获取ID
  */
@Autowired
private ApiBootSequenceContext apiBootSequenceContext;

/**
  * 获取下一个分布式ID的值
  * <p>
  * 返回{@link String}类型的值
  *
  * @return 下一个ID的值
  */
public String nextStringId() {
  return String.valueOf(this.nextId());
}

/**
  * 获取下一个分布式ID的值
  * <p>
  * 返回{@link Long} 类型的值
  *
  * @return 下一个ID的值
  */
public Long nextId() {
  return this.sequence.nextId();
}

```

```Java
// 获取下一个String类型的ID
String nextId = apiBootSequenceContext.nextStringId();
// 获取下一个Long类型的ID
Long nextLongId = apiBootSequenceContext.nextId();
// 输出结果：289894597327331328,289894597327331329
System.out.println(nextId + "," + nextLongId);
```

