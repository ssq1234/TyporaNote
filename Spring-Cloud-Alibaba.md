# Spring-Cloud-Alibaba

## 一.环境搭建

> 引入依赖

```xml
<properties>
        <java.version>1.8</java.version>
        <spring.cloud.alibaba.version>2.2.1.RELEASE</spring.cloud.alibaba.version>
</properties>
```

```xml
<dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring.cloud.alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
</dependencyManagement>
```

## 二.Nacos

### 1.client配置

```xml
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```



```properties
server.port=8789
spring.application.name=nacosclient
spring.cloud.nacos.server-addr=sunshuqiang.top:8848
spring.cloud.nacos.discovery.server-addr=${spring.cloud.nacos.server-addr}
#暴露所有web端点
management.endpoint.web.exposure.include=*
```

### 2.配置中心配置

```xml
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
 </dependency>
```

> 配置文件要修改为bootstrap 否则报错

> 判断配置是否刷新的方法是通过将配置进行MD5加密后进行比较然后判断的

![image-20210129211710286](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210129211710286.png)

```properties
spring.application.name=configclient
spring.cloud.nacos.server-addr=sunshuqiang.top:8848
#指定拉去配置文件的位置
spring.cloud.nacos.config.server-addr=${spring.cloud.nacos.server-addr}
#指定分组
spring.cloud.nacos.config.group=DEFAULT_GROUP
#指定
spring.profiles.active=prod
#指定文件扩展名
spring.cloud.nacos.config.file-extension=properties
```



## 三.服务调用

> 仍旧使用老版组件

openfegin在使用时需要额外引入，并且需要引入cloud官方版本，这样就不用填写版本号