> # **SpringCloud**
>

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



**关闭eureka自我保护机制（不建议**）（15分钟内心跳比例小于85% 服务将会被保护起来 避免出现网络波动阻塞等问题误以为服务宕机）**

![yd3](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd3.png)

**启动类上加注解**

```java
@EnableEurekaServer
```



*说明自己是注册中心**

**访问对应端口进入eureka管理**

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

配置环境变量为  文件位置

目录进入cmd 运行 consul agent -dev

配置完成后访问8500端口

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

同时需要引入spring-cloud依赖

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

Eureka（AP）  Consul（CP） Zookeeper（CP）

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

**private** LoadBalancerClient **loadBalancerClient**;

ServiceInstance products1 = **loadBalancerClient**.choose(**"products"**);

System.**out**.println(products1.getHost());

System.**out**.println(products1.getPort());
```

#### **(3).@LoadBalanced**  **带有****Ribbon轮询****负载均衡的****RestTemplate****对象**

**编写配置类**

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



**使用**

```java
String forObject1 = restTemplate.getForObject("http://项目名称/product/showMsg", String.class);
```



#### **(4).负载均衡策略**

![yd6](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd6.png)

#### **(5).修改负载均衡策略**

**调用需要负载均衡服务名称.ribbon.NFLoadBalancerRuleclassName** =**com.netflix.loadbalancer.RandomRule****（策略类）  （IRule  ctrl+h 查看有哪些策略实现类****）**

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

一个参数时和GET是一致的

当参数为对象时全部需要加上

```java
@RequestBody
```



![yd7](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd7.png)

![yd8](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd8.png)

#### **(7).超时设置**

OpenFeign 默认请求超过一秒  算为请求失败

![yd9](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd9.png)

据说标注字体后续版本必须为大写

#### **(8).日志**

![yd10](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd10.png)

## **五.微服务保护组件**

### **1.Hystrix（Netflix 停止维护）**

#### **(1).服务雪崩**

![yd11](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd11.png)

#### **(2).****雪崩的解决方案****服务熔断（自动 站在链路层面）**

![yd12](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd12.png)

#### **(3).服务降级（人工干预  站在系统层面）**

![yd13](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd13.png)

服务熔断时服务降级的一个小分支，所以熔断必触发降级。