

> # **SpringCloud**

## **一.版本兼容**

![yd1](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd1.png)

![yd2](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd2.png)

## **二.环境配置**

```xml
<properties>
    <java.version>1.8</java.version>
    <spring.cloud-version>Hoxton.SR6</spring.cloud-version>
</properties>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring.cloud-version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
Spring .RELEASE版本是不稳定的 需指定Spring仓库
<repositories>
    <repository>
        <id>spring-snapshots</id>
        <url>http://repo.spring.io/libs-snapshot</url>
    </repository>
</repositories>

<pluginRepositories>
    <pluginRepository>
        <id>spring-snapshots</id>
        <url>http://repo.spring.io/libs-snapshot</url>
    </pluginRepository>
</pluginRepositories>
```



## **三.服务注册中心**

### **1.Eureka（停止维护）**

#### **(1)配置server**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```



```properties
#设置端口
server.port=8761
#起名字
spring.application.name=eureka_server
#指定地址
eureka.client.service-url.defaultZone = http://localhost:8761/eureka
#启动时不立即注册服务 防止启动时发现不了自己（服务） 而报错
eureka.client.fetch-registry=false
#关闭注册自己
eureka.client.register-with-eureka=false
```



> **关闭eureka自我保护机制（不建议**）（15分钟内心跳比例小于85% 服务将会被保护起来 避免出现网络波动阻塞等问题误以为服务宕机）**

![yd3](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd3.png)

> **启动类上加注解**说明自己是注册中心

```java
@EnableEurekaServer
```

> **访问对应端口进入eureka管理**

#### **(2)配置client**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```



```properties
server.port=8888
spring.application.name=eureka_client
#指定注册中心
eureka.client.service-url.defaultZone = http://localhost:8761/eureka
```



**启动类上加注解**

```java
@EnableEurekaClient
```

**说明自己是客户端**

### **2.Consul（作为系统服务单独启动）**

#### **(1)服务端配置**

> 配置环境变量为  文件位置
>
> 目录进入cmd 运行 consul agent -dev
>
> 配置完成后访问8500端口
>

#### **(2)客户端配置**

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
<!--        必须引入健康检查依赖 如果不引入将获取不到服务状态-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

> 同时需要引入spring-cloud依赖
>

```properties
server.port=8889
spring.application.name=consul_client
spring.cloud.consul.host=localhost
spring.cloud.consul.port=8500
#关闭或开启健康检查 建议开启
spring.cloud.consul.discovery.register-health-check=true
#consul 中修改名称 覆盖项目名称
spring.cloud.consul.discovery.service-name=${spring.application.name}
```



### **3.不同注册中心的区别**

![yd4](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd4.png)

> Eureka（AP）  Consul（CP） Zookeeper（CP）
>

## **四.服务间的调用**

![yd5](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd5.png)

### **1.基于RestTemplate的服务调用（url是写死的没有经过注册中心，并且完成不了服务的负载均衡）**

```java
@Autowired
private DiscoveryClient discoveryClient;、
List<ServiceInstance> products = discoveryClient.getInstances("products");
products.forEach(a->{
    System.out.println(a.getHost());
});
```



### **2.Ribbon（consul依赖内已经引入，已经停止维护，但还在大规模生产部署）**

#### **(1).根据名称获取服务列表（从注册中心获取并缓存到客户端）**

```java
@Autowired
private LoadBalancerClient loadBalancerClient;
ServiceInstance products1 = loadBalancerClient.choose("products");
System.out.println(products1.getHost());
System.out.println(products1.getPort());
```



#### **(2).轮询负载均衡**

```java
@Autowired

private LoadBalancerClient loadBalancerClient;

ServiceInstance products1 = loadBalancerClient.choose("products");

System.out.println(products1.getHost());

System.out.println(products1.getPort());
```

#### **(3).@LoadBalanced**  带有Ribbon轮询负载均衡的RestTemplate对象

> **编写配置类**

```java
@Configuration
public class RestTemplateConfig {

    //在工厂中创建一个RestTemplate对象
    @Bean
    @LoadBalanced  //带有Ribbon负载均衡的RestTemplate对象
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```



> **使用**

```java
String forObject1 = restTemplate.getForObject("http://项目名称/product/showMsg", String.class);
```



#### **(4).负载均衡策略**

![yd6](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd6.png)

#### **(5).修改负载均衡策略**

> **调用需要负载均衡服务名称.ribbon.NFLoadBalancerRuleclassName** =com.netflix.loadbalancer.RandomRule（策略类）  （IRule  ctrl+h 查看有哪些策略实现类）

### **3.OpenFeign**

#### **(1).引入依赖**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```



#### **(2).启动类上加注解**

```java
@EnableFeignClients 
```



#### **(3).按服务配置接口**

```java
@FeignClient(value = "products")   //用来表示当前接口是一个fegin的组件 value 书写调用的服务
public interface ProductClient {
    @GetMapping("product/showMsg")  //填写请求接口
    String ShowMsg();   //此处和原接口处保持一致
}
```



#### **(4).使用**

```java
@Autowired
private ProductClient productClient;

String msg = productClient.ShowMsg();
```



#### **(5).GET方式参数传递**

```java
@GetMapping("/product/findOne")   //使用get传递参数一定要加上@RequestParam
Map<String, Object> findOne(@RequestParam("productId") String productId);
Map<String, Object> one = productClient.findOne(id);
```



#### **(6).POST方式参数传递**

> 一个参数时和GET是一致的
>
> 当参数为对象时全部需要加上
>

```java
@RequestBody
```

> 当参数为文件时

```java
@RequestPart("")  //除此之外还可能有其他配置
```



![yd7](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd7.png)

![yd8](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd8.png)

#### **(7).超时设置**

> OpenFeign 默认请求超过一秒  算为请求失败
>

![yd9](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd9.png)

> 据说标注字体后续版本必须为大写
>

#### **(8).日志**

![yd10](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd10.png)

## **五.微服务保护组件**

### **1.Hystrix（Netflix 停止维护）**

#### **(1).服务雪崩**

![yd11](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd11.png)

#### (2).雪崩的解决方案服务熔断（自动 站在链路层面）

![yd12](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd12.png)

#### **(3).服务降级（人工干预  站在系统层面）**

![yd13](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd13.png)

> 服务熔断时服务降级的一个小分支，所以熔断必触发降级。

#### (4).引入Hystrix

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

```java
@EnableCircuitBreaker  //启动类加注解
```

#### (5).服务熔断使用

```java
	@GetMapping("/product/break")
    //@HystrixCommand(fallbackMethod = "testBreakFallBack")
	@HystrixCommand(defaultFallback = "testBreakFallBack1")
    public String testBreak(Integer id) {
        if (id < 0) {
            throw new RuntimeException("id不能小于0");
        }
        return "访问成功,id:" + id;
    }
    //触发熔断的方法
    public String testBreakFallBack(Integer id){
        return "当前传入的id不是有效参数,触发熔断";
    }
	//默认触发熔断的方法
    public String testBreakFallBack1(){
        return "当前传入的id不是有效参数,触发熔断";
    }
```

![image-20210116173236537](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210116173236537.png)

> yu值

#### (6).服务降级使用

> 首先注意服务端的熔断优先级要高于客户端的熔断，一定要保证两处熔断的返回类型相同

> 加入配置

```properties
feign.hystrix.enabled=true
```

```java
@FeignClient(value = "products",fallback = ProductClientFallBack.class)   //加入fallback 
//而fallbackfactory有异常信息
//类一定是当前接口的实现类且一定要注入到Spring工厂
public interface ProductClient {
    @GetMapping("/product/break")
    public Map<String, Object> testBreak(@RequestParam("id") Integer id);
}
```

```java
@Component
public class ProductClientFallBack implements ProductClient{
    @Override
    public Map<String, Object> testBreak(Integer id) {
        Map<String, Object> r = new HashMap<>();
        r.put("status","false");
        r.put("msg","当前服务已被降级");
        return r;
    }
}
```

#### (7).仪表盘

> 新建仪表盘项目使用仪表盘

## 六.微服务网关

### 1.GateWay组件

> 配置，也可以使用Java配置类的形式进行配置，官方建议使用配置文件

```yml
server:
  port: 8989
spring:
  application:
    name: gateway
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
    gateway:
      routes:
        - id: users_router    #指定路由唯一标识
          uri: http://localhost:9999/   #指定地址
          predicates:
            - Path=/user/**     #把以user开头的请求转发到用户服务（断言）
```

> 依赖

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

> 同时需要引入Spring-Cloud Consul等依赖，还需要移除以下依赖

```xml
<!--        使用网关不能使用该依赖  因为gateway网关是基于spring-webflux 会照成冲突-->
<!--        <dependency>-->
<!--            <groupId>org.springframework.boot</groupId>-->
<!--            <artifactId>spring-boot-starter-web</artifactId>-->
<!--        </dependency>-->

```

#### (1).实现负载均衡路由转发

> 直接修改uri为

```yml
uri: lb://users  ->  服务名称
```

#### (2).Predicate  断言

```yml
 - Path=  /user/**    #把以user开头的请求转发到用户服务（断言）
 #ZonedDateTime.now() 获取区域时间  以下代表时间之后才能访问对应的服务
 - After=2021-01-17T20:21:00.068261300+08:00[Asia/Shanghai]
 #以下代表时间之前才能访问对应的服务
 - Before=2021-01-17T20:23:00.068261300+08:00[Asia/Shanghai]
 #以下代表时间段之内才能访问对应的服务
 - Between=2021-01-17T20:21:00.068261300+08:00[Asia/Shanghai],2021-01-	      17T20:23:00.068261300+08:00[Asia/Shanghai]
 #以下为http请求头内携带cookie信息，且有键值对username=ssq
 - Cookie=username,ssq
 #以下为http请求头内携带cookie信息，且有键值对username={满足正则表达式}
 - Cookie=username,[A-Za-z0-9]+
 #请求头中有以下蛮族条件的键值对
 - Header=id,\d+
 #必须是以下请求方式
 - Method=GET,POST
```

#### (3).Filters 路由筛选器（权限过滤器）

> 内置过滤器使用不多，一般使用自定义过滤器

![image-20210117210921235](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210117210921235.png)

> 其中可以替换为自己的逻辑，例如JWT

## 七.Config组件

![image-20210118190250212](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210118190250212.png)

### 1.Config Server

> 引入spring-cloud，consul，config-server

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

> 启动类加入注解

```java
@EnableConfigServer
```

> 配置

```properties
server.port=7878
spring.application.name=configserver
spring.cloud.consul.port=8500
spring.cloud.consul.host=localhost
spring.cloud.consul.discovery.service-name=${spring.application.name}
#设置远程git地址,注意仓库一定是公共的，且为http协议
#如果是私有的则需要配置账号密码
spring.cloud.config.server.git.uri=https://github.com/ssq1234/configserver.git
#修改本地仓库存储目录(一定要注意文件夹要为空文件夹，因为启动时会清空该文件夹里的所有内容)
spring.cloud.config.server.git.basedir=C:/Users/sunshuqiang/Desktop/项目/springCloud/test/configserver7878/src/main/resources/localconfig
#指定分支，GitHub目前默认分支叫做main
spring.cloud.config.server.default-label=main
```

启动后访问

> http://localhost:7878/orders-xxx.properties
>
> orders为文件名 xxx为属于哪一种环境下的配置（开发，测试，上线...）
>
> 如果xxx找不到则默认访问orders.properties(公共配置文件，一些基础的公共的配置都在这里)
>
> 后缀也可以改成yml或者json，会自动转换为对应的格式

### 2.Config Client

![image-20210119202907187](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210119202907187.png)

> 因为需要预加载配置所以配置文件应该修改名称为bootstrap.properties或者yml
>
> 该配置文件的优先级要高于application.properties或者yml

![image-20210119205008224](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210119205008224.png)

```properties
#开启从远端找配置
spring.cloud.config.discovery.enabled=true
#按照这个名称的配置中心去拉去配置
spring.cloud.config.discovery.service-id=configserver
#设置GitHub分支
spring.cloud.config.label=main
#公共配置文件名
spring.cloud.config.name=client
#独有配置文件名
spring.cloud.config.profile=prod
#一定要加上服务名称，负责会导致无法在consul注册
spring.application.name=configclient
```

> 手动配置刷新

```properties
#开启所有web端点暴露，以便可以接受刷新配置的post请求
management.endpoints.web.exposure.include=*
```

> http://localhost:7879/actuator/refresh
>
> 对单个服务进行post请求，通知进行配置刷新
>
> 但只能进行整体的配置刷新，如果你注入了配置内容则需要在类上加上注解,这样注入的信息才会在代码中发生改变

```java
@RefreshScope
```

## 八.消息总线（BUS）（目前连不上，可以替换为阿里巴巴解决方案）

### 1.RabbitMQ

> erlang安装包

```apl
https://packages.erlang-solutions.com/erlang/rpm/centos/7/x86_64/esl-erlang_22.1-1~centos~7_amd64.rpm
```

> rabbitmq安装包

https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.0/rabbitmq-server-3.8.0-1.el7.noarch.rpm

> erlang rpm方式安装

#### (1).安装依赖项

```shell
yum install -y epel-release
```

#### (2).下载rpm包

```shell
wget https://packages.erlang-solutions.com/erlang/rpm/centos/7/x86_64/esl-erlang_22.1-1~centos~7_amd64.rpm
```

#### (3).安装

```shell
yum install esl-erlang_22.1-1~centos~7_amd64.rpm
```

#### (4).验证

```shell
erl -version
```

> 出现“Erlang (SMP,ASYNC_THREADS,HIPE) (BEAM) emulator version 10.5”证明安装成功

> rabbitmq rpm安装

#### (1).安装依赖项

```shell
yum install rabbitmq-server-3.8.0-1.el7.noarch.rpm
```

#### (2).开机启动及启动

```shell
chkconfig rabbitmq-server on
systemctl start rabbitmq-server
```

#### (3).检查 

```shell
rabbitmqctl status
```

#### (4)启用UI以及PROMETHEUS插件

```bash
# 启用插件
rabbitmq-plugins enable rabbitmq_management rabbitmq_prometheus

# 查看启用的插件
rabbitmq-plugins list
.... 省略其他
[E*] rabbitmq_management               3.8.7
[e*] rabbitmq_management_agent         3.8.7
[E*] rabbitmq_prometheus               3.8.7
[e*] rabbitmq_web_dispatch             3.8.7

# 重启服务
systemctl restart rabbitmq-server.service

netstat -pltn
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:25672           0.0.0.0:*               LISTEN      3081/beam.smp       
tcp        0      0 0.0.0.0:15692           0.0.0.0:*               LISTEN      3081/beam.smp  # prometheus metrics     
tcp        0      0 0.0.0.0:4369            0.0.0.0:*               LISTEN      3211/epmd                  
tcp        0      0 0.0.0.0:15672           0.0.0.0:*               LISTEN      3081/beam.smp       
tcp6       0      0 :::5672                 :::*                    LISTEN      3081/beam.smp    

# 验证metrics API
curl localhost:15692/metrics

# 验证UI
浏览器访问 10.122.151.8:15672 （本地IP地址 10.122.151.8）
备注：当前版本guest/guest默认只支持localhost访问[User can only log in via localhost]，如果需要可以修改
```

#### (5)创建admin账户，设置角色，并授权

这里授予做大权限了，可以根据实际需要进行授权

```bash
rabbitmqctl add_user admin admin
rabbitmqctl set_user_tags admin  administrator
rabbitmqctl set_permissions -p / admin  ".*" ".*" ".*"
```