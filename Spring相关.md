> # **Spring相关**
>

## 一.**注解相关**

### 1.请求参数区数据映射到功能处理方法的参数：

```java
@RequestParam(value="")
```

### 2.spring提供注入-按类型注入：

```java
@Autowired
```

### 3.Javaee提供注入-先按名称注入，找不到名称按类型注入

```java
@Resource
```

### 4.当一个接口有多个实现类时，就可以用此注解表明哪个实现类才是我们所需要的

```java
@Qualifier("name")
```

### 5.spring对象管理单例还是多例，默认为单例

```java
@scope("singleton") 
```

### 6.多例

```java
@scope("prototype")
```

### 7.日志注解

```java
@Slf4j 
log.info("")
```

### 8.注入端口

```java
@Value("${server.port}")
private int port;
```

### 9.将Json型数据转换为对象

```java
@RequestBody
```

