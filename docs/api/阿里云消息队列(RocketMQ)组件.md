

### 阿里云消息队列(RocketMQ)组件



### 一、RocketMQ相关说明

* 快速入门：<https://help.aliyun.com/document_detail/34411.html>

* [收发顺序消息](https://help.aliyun.com/document_detail/49323.html?spm=a2c4g.11186623.6.602.HOqOX3)

* [收发事务消息](https://help.aliyun.com/document_detail/29548.html?spm=a2c4g.11186623.6.603.LFUtTE)

* [收发延时消息](https://help.aliyun.com/document_detail/29549.html?spm=a2c4g.11186623.6.604.sCq6lR)

* [收发定时消息](https://help.aliyun.com/document_detail/29550.html?spm=a2c4g.11186623.6.605.Pxqkpc)

  

> RocketMQ 网络部署特点

* Name Server 是一个几乎无状态节点，可集群部署，节点之间无任何信息同步。
* Broker 部署相对复杂，Broker 分为 Master 与 Slave，一个 Master 可以对应多个 Slave，但是一个 Slave 只能对应一个 Master，Master 与 Slave 
  的对应关系通过指定相同的 BrokerName，不同的 BrokerId 来定义，BrokerId为 0 表示 Master，非 0 表示 Slave。Master 也可以部署多个。每个 Broker 
  与Name Server 集群中的所有节点建立长连接，定时注册 Topic 信息到所有 Name Server。

* Producer 与 Name Server 集群中的其中一个节点(随机选择)建立长连接，定期从 Name Server 取 Topic 路由信息，并向提供 Topic 服务的 Master 
  建立长连接，且定时向 Master 发送心跳。Producer 完全无状态，可集群部署。

* Consumer 与 Name Server 集群中的其中一个节点(随机选择)建立长连接，定期从 Name Server 取 Topic 路由信息，并向提供 Topic 服务的 Master、
  Slave 建立长连接，且定时向 Master、Slave 发送心跳。Consumer 既可以从 Master 订阅消息，也可以从 Slave 订阅消息，订阅规则由 Broker 配置决定。  

### 二、接入概要说明

> 1、通用参数说明

| 参数名    | 参数说明                                                 |
| --------- | -------------------------------------------------------- |
| onsAddr   | 设置 MQ TCP 协议接入点，参考上面表格（推荐）             |
| AccessKey | 您在阿里云账号管理控制台中创建的 AccessKey，用于身份认证 |
| SecretKey | 您在阿里云账号管理控制台中创建的 SecretKey，用于身份认证 |
| groupId   | 您在阿里云账号管理控制台中创建的groupId,用户发送订阅消息 |

> 2、发送消息参数说明

| 参数名                                 | 参数说明                                         |
| -------------------------------------- | ------------------------------------------------ |
| groupId                                | 您在控制台创建的 groupId                         |
| SendMsgTimeoutMillis                   | 设置消息发送的超时时间，单位（毫秒），默认：3000 |
| CheckImmunityTimeInSeconds（事务消息） | 设置事务消息第一次回查的最快时间，单位（秒）     |
| shardingKey（顺序消息）                | 顺序消息中用来计算不同分区的值                   |

> 3、订阅消息参数说明

| 参数名            | 参数说明                                                     |
| ----------------- | ------------------------------------------------------------ |
| ConsumerId        | 您在 MQ 控制台上创建的 groupId                               |
| MessageModel      | 设置 Consumer 实例的消费模式，默认为集群消费（值：CLUSTERING）;广播消费（BROADCASTING） |
| ConsumeThreadNums | 设置 Consumer 实例的消费线程数，默认：64                     |
| MaxReconsumeTimes | 设置消息消费失败的最大重试次数，默认：16                     |
| ConsumeTimeout    | 设置每条消息消费的最大超时时间，超过设置时间则被视为消费失败，等下次重新投递再次消费。每个业务需要设置一个合理的值，单位（分钟）。默认：15 |
| suspendTimeMillis | （顺序消息）	只适用于顺序消息，设置消息消费失败的重试间隔时间 |

> 4、TCP协议接入域名

| 环境说明                                | 接入点                                                       |
| --------------------------------------- | ------------------------------------------------------------ |
| 公共云内网接入                          | （阿里云经典网络/VPC）：                                     |
| 华东1、华东2、华北1、华北2、华南1、香港 | http://onsaddr-internal.aliyun.com:8080/rocketmq/nsaddr4client-internal |
| 公共云公网接入（非阿里云选择这个）      | http://onsaddr-internet.aliyun.com/rocketmq/nsaddr4client-internet |
| 公共云 Region：亚太东南1(新加坡)        | http://ap-southeastaddr-internal.aliyun.com:8080/rocketmq/nsaddr4client-internal |
| 公共云 Region：亚太东南3(吉隆坡)        | http://ons-ap-southeast-3-internal.aliyun.com:8080/rocketmq/nsaddr4client-internal |
| 公共云 Region：亚太东北 1 (东京)        | http://ons-ap-northeast-1-internal.aliyun.com:8080/rocketmq/nsaddr4client-internal |
| 金融云 Region：华东1                    | http://jbponsaddr-internal.aliyun.com:8080/rocketmq/nsaddr4client-internal |
| 金融云 Region：华东2、华南1             | http://mq4finance-sz.addr.aliyun.com:8080/rocketmq/nsaddr4client-internal |


### 三、starter的基本用法：

> 1、pom.xml引入依赖

```xml
        <dependency>
            <groupId>org.yxj.framework</groupId>
            <artifactId>spring-starter-alimq</artifactId>
            <version>1.0</version>
        </dependency>
```

也可以使用旧版本依赖【使用效果一致】

```xml
        <!-- Mq config ali组件 -->
        <dependency>
            <groupId>cn.knowbox.book</groupId>
            <artifactId>spring-boot-starter-alimq</artifactId>
            <version>2.0.0.RELEASE</version>
        </dependency>
```


> 2、application配置文件中添加相应配置


```yaml
 aliyun:
    mq:
        accessKey: xxx
        secretKey: xxx
        # 配置地址
        onsAddr: http://MQ_INST_1683763996649660_BaQTTs9E.mq-internet-access.mq-internet.aliyuncs.com:80
        config:
            # 是否开启
            enabled: true
            # 阿里配置中心新增的GID
            groupId: xxx
    
```



> 3、 生产者封装的工具类及示例

```java
/**
 * @author kk
 * @version 1.0
 * @desc 生产者-通用方法
 * @date 2018/7/7 下午5:19
 */
@Slf4j
public class RocketMQTemplate {

    @Resource
    private ProducerBean producer;

    /****
     * @Description: 同步发送
     * @Param: [event]
     * @Author: kk
     */
    public SendResult send(MessageEvent event) {}

    /****
     * @Description: 同步发送(带延迟时间)
     * @Param: [event, delay]
     * @Author: kk
     */
    public SendResult send(MessageEvent event, long delay) {}

    /**
     * @Description: 单向发送
     * @Param: [event]
     * @Author: kk
     */
    public void sendOneway(MessageEvent event) {}

    /**
     * @Description: 异步发送
     * @Param: [event]
     * @Author: kk
     */
    public void sendAsync(MessageEvent event) {}


    /**
     * @Description: 异步发送(带延迟时间)
     * @Param: [event, delay]
     * @Author: kk
     */
    public void sendAsync(MessageEvent event, long delay) {}


}

```

```java

@Service
public class ALiService {
	 @Autowired
   private RocketMQTemplate rocketMQTemplate;
   
    /**
     * 普通消息生产者-调用
     */
    public void sentMsg() {
        /**封装消息*/
        MessageEvent event = new MessageEvent();
        event.setTopic("base_sms");
        event.setTag("Tag_user");
       
        User user = new User();
        user.setName("Paul");
        user.setAdds("北京市 昌平区 龙锦苑东二区");
         /**封装任意类型领域对象*/
        event.setDomain(user);
        
        rocketMQTemplate.send(event);
    }
}

```

```java

@Service
public class ALiService {
	 @Autowired
   private OrderMessageTemplate rocketMQTemplate;
   
    /**
     * 顺序消息生产者-调用
     */
    public void sentMsg() {
        /**封装消息*/
        MessageEvent event = new MessageEvent();
        event.setTopic("base_sms");
        event.setTag("Tag_user");
       
        User user = new User();
        user.setName("Paul");
        user.setAdds("北京市 昌平区 龙锦苑东二区");
         /**封装任意类型领域对象*/
        event.setDomain(user);
        
        rocketMQTemplate.send(event);
    }
}

```

```java

@Service
public class ALiService {
	@Autowired
	private TransactionMessageTemplate transactionMessageTemplate;
	
  @PostConstruct
	public void init() {
		//发事务消息前需要初始化LocalTransactionCheckerImpl
		transactionMessageTemplate.init(new TransactionChecker() {
			
			@Override
			public TransactionStatus check(MessageEvent messageEvent, Long hashValue) {
				TransactionStatus status = TransactionDemo.checker();
				System.out.println("sysout TransactionChecker.check 方法被调用:"+JSON.toJSONString(messageEvent));
				return status;
			}
		});
		log.info("初始化 LocalTransactionChecker 完成");
	}
	
    /**
     * 事务消息生产者-调用
     */
    public void sentMsg() {
        /**封装消息*/
        MessageEvent event = new MessageEvent();
        event.setTopic("base_sms");
        event.setTag("Tag_user");
       
        User user = new User();
        user.setName("Paul");
        user.setAdds("北京市 昌平区 龙锦苑东二区");
         /**封装任意类型领域对象*/
        event.setDomain(user);
        
        transactionMessageTemplate.send(event,new TransactionExecuter() {
			
					@Override
					public TransactionStatus executer(MessageEvent messageEvent, Long hashValue, Object arg) {
						String transactionId = TransactionDemo.createTransaction();
						TransactionStatus status = TransactionDemo.checker();
						return status;
					}
				),"参数对象,以本字符串示例,会传递给TransactionExecuter.executer");
    }
}

```

> 4、 consumer监听处理类实现，继承AbstractMessageListener类，实现handle方法即可，如：

```java

/**
 * @author kk
 * @version 1.0
 * @desc 用户事件监听处理
 * @desc 可以单独监听topic,也可以根据tag区分业务领域
 * @date 2018/7/5 上午11:18
 */

@Service
@RocketMQMessageListener(topic = "base_sms", tag = {"test"})
public class UserMessageListener extends AbstractMessageListener<MessageEvent> {
    /**
     * 消息处理
     */
    @Override
    public void handle(MessageEvent messageEvent) {
	    log.info("=========== 开始处理 {} 消息========", messageEvent.getTopic());
        System.out.println(messageEvent.toString());
    }
}

```

* topic为必填，可以配置多个不同topic监听业务处理

> 5、优点

* 1、使用简单：开箱即用,只需要简单配置
* 2、分业务处理：针对同一topic、通过tag区分具体业务场景。		


### 相关参考

----

* 官网Demo地址：<https://github.com/AliwareMQ/mq-demo>
* Apache的starter：<https://github.com/apache/rocketmq-externals/tree/master/rocketmq-spring-boot-starter>
