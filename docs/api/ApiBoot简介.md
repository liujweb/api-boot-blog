ApiBoot是接口服务的落地解决方案，提供了一系列开箱即用的组件，通过封装来简化主流第三方框架的集成，从而提高开发者开发效率、学习成本、降低入门门槛，真正的实现开箱即用！！！

对SpringBoot简单了解的开发者就可以编写安全稳定的接口服务，可为移动端、网页端等多个端点提供丰富的安全接口。

ApiBoot依赖于SpringBoot，可以使用ApiBoot构建独立的Java应用程序。

愿景：

- 为后端Java开发者提供低门槛第三方框架集成解决方案，让复杂的框架集成使用的门槛更低。
- 开箱即用，内部封装了主流框架，只需添加依赖、简单配置即可使用。
- 各个组件可独立使用，不再冗余你的应用程序。
- 可简单快速的构建安全的restful资源接口服务。
- 可用于构建SpringCloud微服务服务实例。


### 安装 & 入门

```xml
<dependencyManagement>
  <dependencies>
    <!--ApiBoot版本依赖-->
    <dependency>
      <groupId>org.minbox.framework</groupId>
      <artifactId>api-boot-dependencies</artifactId>
      <version>${lastVersion}</version>
      <scope>import</scope>
      <type>pom</type>
    </dependency>
  </dependencies>
</dependencyManagement>
```

