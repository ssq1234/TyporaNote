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

> **所有的子项目一定要引入他爹是谁不然他找不到，我操你妈IDEA    <-  这傻逼干的**

```xml
<parent>
    <groupId>com.cust</groupId>
    <artifactId>para</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
```



## 二.Nacos配置中心

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


#指定命名空间
#spring.cloud.nacos.config.namespace=
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

## 四.Sentinel流量卫兵

### 1.引入依赖

```xml
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

> QPS ：每秒请求数

> 配置

```properties
spring.cloud.sentinel.transport.dashboard=sunshuqiang.top:9191
spring.cloud.sentinel.transport.port=8719
```



> sentinel要跑在本地才可以，若要跑在云端，则微服务需要配置，否则实时监控无效

```properties
spring.cloud.sentinel.transport.clientIp=微服务所在的地址
```

### 2.流控规则

#### (1).直接限流

> 当QPS>1时，直接限流

#### (2).关联限流

![image-20210130211107158](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210130211107158.png)

> 例如两种请求分别对数据库进行读和写的操作，如果任凭两者抢夺资源，则会降低吞吐量，所以设置关联模式，当/test/test1 在一秒内QPS>时  对/test/test进行限流

#### (3).链路限流

![image-20210130211914467](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210130211914467.png)

> 当/product/orderFindAll QPS>1时，将会限制/product/orderFindAll 对 /order/findAll的调用

> **应用场景: 比如支付接口（/product/orderFindAll）达到阈值,就要限流下订单的接口（/order/findAll）,防止一直有订单**

#### (4).流控效果

#### ![image-20210130214955480](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210130214955480.png)       

#### (5).降级策略

![image-20210131130535355](Spring-Cloud-Alibaba.assets/image-20210131130535355.png)

![image-20210131132311001](Spring-Cloud-Alibaba.assets/image-20210131132311001.png)

## 五.架构说明

![image-20210131133256538](Spring-Cloud-Alibaba.assets/image-20210131133256538.png)

> 服务网关也要做服务的集群