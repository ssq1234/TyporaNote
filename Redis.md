# Redis

> Redis由C语言编写 是单线程的 ==速度很快==基于内存操作） 所以CPU不是性能瓶颈	而是内存和带宽
>
> 选择单线程的原因  
>
> 1.多线程不一定要比单线程快  因为多线程需要上下文切换
>
> 2.因为Redis是内存型数据库 所以选择单线程是最好的

## 一.常用命令

### 1.切换数据库

> 默认有16个数据库

```bash
select 3
```

### 2.查看数据库大小

```bash
dbsize
```

### 3.查看数据库所有的key

```bash
keys *
```

### 4.清空当前数据库

```bash
flushdb
```

### 5.清空全部数据库

```bash
flushall
```

### 6.判断当前键是否存在

```bash
exists name
```

### 7.将当前数据库key移动到另一个数据库

```bash
move key 1;
```

### 8.设置键过期时间

```bash
expire name 10
```

### 9.查看key剩余时间

```bash
ttl key
```

### 10.查看数据类型

```bash
type name
```

## 二.String类型

```bash
set key value  #添加一条数据
get key		#获取值
append key “hello”		#在字符串后追加字符
strlen key		 #获取value长度
insr key 		#数字自增
decr key		#数字自减
incrby key 大小		#自增指定大小
decrby key 大小		#自减指定大小
getrange key 0 1		#截取0到1的字符
getrange key 0 -1		#从头到尾截取全部字符串
setrange key 1 “xx”		#替换指定位置开始的字符串    hello -> hxxlo
setex key 过期时间 value		#存入值并设置过期时间
setnx key value		#如果不存在再添加 在分布式锁当中常常使用
mset name 1 name 2		#存入多个值
msetnx key value key1 value1		#如果（都！）不存在再添加 具有原子性
mget key key1 key3		#取多个值
mset user:name zhangsan user:age 14		#可以用这种高阶操作存储对象
mget user:name user:age		#可以用这种高阶操作获取对象值
getset key value		#返回旧值存入新值 如果不存在旧值返回null
```



## 三.事务

> Redis事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行
>
> 一次性、顺序性、排他性

**Redis事务没有隔离级别的概念**

**Redis单条命令是==原子性==的，但是事务不保证原子性**（见发生运行时异常时）

### 1.正常执行事务

```bash
开启事务：
multi
一些命令
...
exec
```

### 2.放弃事务

```bash
开启事务：
multi
一些命令
...
DISCARD
```

### 3.发生编译型异常  一些命令发生了错误

> 此时所有命令都不会被执行

### 4.发生运行时异常

> 发生错误的命令不会被执行 其他正常的命令依旧会执行
>
> ==此时无法保证原子性==

