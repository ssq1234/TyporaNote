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

### 1.配置

```properties
server.port=8789
spring.application.name=nacosclient
spring.cloud.nacos.server-addr=sunshuqiang.top:8848
spring.cloud.nacos.discovery.server-addr=${spring.cloud.nacos.server-addr}
#暴露所有web端点
management.endpoint.web.exposure.include=*
```