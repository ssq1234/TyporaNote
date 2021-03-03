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
del key		#删除值
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

## 三.List类型

```bash
lpush key one		#从左插值
(integer) 1
lpush key two
(integer) 2
lpush key three
(integer) 3
lrange key 0 -1		#正着进倒着出
1) "three"
2) "two"
3) "one" 		
rpush key 123		#从右插入值
lpop key		#从左弹栈
rpop key		#从右弹栈
lindex key index		#获取从左  下标index的值
llist key		#获取list长度
lrem key 个数 值		#移除指定个数的指定值
ltrim key index index		#截取index到index替换掉原来的list
rpoplpush key other		#把key右弹出 弹出的值存到other里
lset key index 值		#把list指定下标的值改变 如果不存在list会报错
linsert key before 123 1		#在list 123值前插入一个值1
linsert key after 123 1		#在list 123值后插入一个值1
```

> 本身其实是一个链表 所以向中间插值效率会略低

> 可以作为消息中间件

## 四.Set类型

> 集合内元素不能够重复

```bash
sadd key value		#插入值
smembers key		#查看集合内所有元素
sismember key value		#查看集合内是否存在value
scard key		#查看一共有多少个值
srem key value		#移除集合中指定的元素
srandmember key		#随机抽取一个值
spop key		#随机弹出一个元素
smove key1 key2 value		#将key1里的value移到key2
sdiff key1 key2		#key1里有的 key2里没有的
sinter key1 key2		#key1和key2交集
sunion key1 key2		#key1和key2并集
```

## 五.Hash类型

> 和String命令很像！

key->{key:value}

```bash
hset key key value		#插入值
hget key key		#取值
hmset key key1 value1 key2 value2		#同时插入多值
hmget key key1 key2		#取多个值
hgetall key		#获取全部键值对
hdel key key		#删除指定的key-value
hlen key		#获取hash长度
hexists key key1		#查看是否由指定的key1
hkeys key		#获取key内所有的key
hvals key		#获取所有的值
hincrby key 大小		#指定数字增加多少
hsetnx key key value		#存在就不插入 不存在才会插入值
```

## 六.Zset类型

> 有序集合

```bash
在set的基础上加上了一个值
zadd key 值 value		#添加value 他有个值
zrange key 0 -1		#取出所有值
127.0.0.1:6379> zadd a 2500 ssq
(integer) 1
127.0.0.1:6379> zadd a 25 ssq1
(integer) 1
127.0.0.1:6379> zadd a 251 ssq12
(integer) 1
127.0.0.1:6379> ZRANGEBYSCORE a -inf +inf		#按照值从小到大排序 -inf（负无穷）
1) "ssq1"
2) "ssq12"
3) "ssq"
ZRANGEBYSCORE a -inf +inf withscores		#值和value都从小到大排序
ZRANGEBYSCORE a -inf 2500		#按照值从小到大排序 值在-inf（负无穷）到2500之内
ZREVRANGE a 0 -1		#按照从大到小排序
zrem key value		#移除一个元素
zcard key		#查看一共多少个元素
```

## 七.Geospatial 地理位置

```bash
geoadd key 经度 纬度 城市名		#添加地理位置
geopos key 城市名		#获取城市的经度纬度
geodist key 城市名 城市名	距离单位	#获取两个城市之间的直线距离
georadius key 经度 纬度 半径 距离单位 [withdist|withcoord] [count 数量]		#以输入的经度纬度为中心 在key中寻找半径范围内的城市[带上和你之间的距离|带上城市的经度纬度] [现实的城市数量]
georadiusbymember key 城市名称 半径 距离单位		#以输入的城市为中心 在key中寻找半径范围内的城市
geohash key 城市名		#返回城市经纬度的哈希字符串
```

> 由于底层是Zset实现 更多命令见Zset

## 八.Hyperloglog 基数统计

> 两个集合中重复的元素叫做基数 可以用来统计网站访问量

==优点==：占用的内存固定

==缺点==：错误率0.81%

```bash
pfadd key 元素......		#添加元素（不重复）
pfcount key		#查看有多少个不重复的元素
pfmerge key3 key1 key2		#把key1 key2做并集放入key3 
```

## 九.Bitmaps 位存储

> 当一个事件只有两个状态时适合使用位存储 内部使用二进制存储 只有0和1
>
> 例如365天打卡 1表示签到 0表示未签到 0 1 0 1

```bash
setbit key 第几天 位		#添加记录值
getbit key 第几天		#查看某一天的记录值
bitcount key		#查看有几个1
```



## 十.事务

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

## 十一.监控 实现乐观锁

```bash
127.0.0.1:6379> watch money		#监视money
OK
#开启一个线程执行一个关于money的事务
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> DECRby money 10
QUEUED
127.0.0.1:6379> INCRby out 10
QUEUED

#当事务还没结束时 此时另外一个线程修改了money
127.0.0.1:6379> get money
"80"
127.0.0.1:6379> set money 1000
OK
#原来的线程结束事务发现失败
127.0.0.1:6379> exec
(nil)
#解除监视 重新监视 重新开启事务
127.0.0.1:6379> unwatch
OK
127.0.0.1:6379> watch money
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> DECRby money 10
QUEUED
127.0.0.1:6379> INCRBY money 10
QUEUED
127.0.0.1:6379> exec
```

## 十二.Jedis

> 导入对应的依赖

```xml
<dependencies>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.2.0</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
</dependencies>
```

```java
package com.cust;
import com.alibaba.fastjson.JSONObject;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;
/**
 * @author 孙术强
 * @date 2021/2/21 19:58
 */
public class TestPing {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("sunshuqiang.top", 6379);
        jedis.auth("123456");
         // jedis所有指令 就是基本指令
        System.out.println(jedis.ping());
         // fastjson存入数据
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("hello",1);
        jsonObject.put("hello1",2);
        Transaction multi = jedis.multi();
         // 开启事务
        try {
            multi.set("key",jsonObject.toJSONString());
            multi.set("key1",jsonObject.toJSONString());
            multi.exec();
            System.out.println(jedis.get("key"));
        } catch (Exception e) {
            multi.discard();
            e.printStackTrace();
        } finally {
            jedis.close();
        }
    }
}
```

## 十三.SpringBoot

> SpringBoot中jedis被替换为了lettuce
>
> jedis是线程不安全的，如果想避免不安全就需要jedis pool连接池 更像BIO
>
> lettuce是线程安全的 采用netty在多个线程中进行共享 可以减少线程数据 更像NIO

==通过操作ops选择操作类型操作redis 可以封装一个工具类==

## 十四.配置文件conf

### 1、单位

```conf
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
```

### 2、配置文件对大小写不敏感

```conf
# units are case insensitive so 1GB 1Gb 1gB are all the same.
```

### 3、可以包含其他的配置文件

```conf
# include /path/to/local.conf
# include /path/to/other.conf
```

### 4、允许哪些ip远程访问

```conf
#bind 0.0.0.0  #允许所有ip访问
```

### 5、保护模式 只允许本地连接

```conf
protected-mode no
```

### 6、端口设置

```conf
port 6379
```

### 7、是否以守护进程运行

```conf
daemonize yes
```

### 8、后台运行时记录进程的pid文件位置

```conf
pidfile /var/run/redis_6379.pid
```

### 9、日志级别

```conf
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice
```

### 10、日志存储文件名

```conf
# Specify the log file name. Also the empty string can be used to force
# Redis to log on the standard output. Note that if you use standard
# output for logging but daemonize, logs will be sent to /dev/null
logfile ""
```

### 11、数据库数量

```conf
databases 16
```

### 12、启动时是否显示logo

```bash
always-show-logo yes
```

### 13、持久化规则

```conf
save 900 1		#九百秒内如果至少有一个key进行过修改 就进行持久化
save 300 10
save 60 10000
```

### 14、持久化错误是否继续工作

```conf
stop-writes-on-bgsave-error yes
```

### 15、是否压缩RDB文件

```conf
rdbcompression yes		#需要消耗cpu资源
```

### 16、保存RDB文件进行错误检查校验

```conf
rdbchecksum yes
```

### 17、RDB文件保存目录

```conf
dir ./
```

### 18、设置密码

```conf
requirepass 123456
```

### 19、最大客户端连接数量

```conf
# maxclients 10000
```

### 20、配置最大内存容量

```conf
# maxmemory <bytes>
```

### 21、内存到达上限处理策略

```conf
# maxmemory-policy noeviction
```

### 22、是否开启AOF

```conf
appendonly no
```

### 23、持久化文件名字

```conf
appendfilename "appendonly.aof"
```

### 24、AOF同步策略

```conf
# appendfsync always	#总是
appendfsync everysec  #每秒
# appendfsync no	#不同步 操作系统自己执行同步 速度最快
```

## 十五.快照持久化（RDB）

> RDB保存的文件为dump.rdb 

> 触发机制

1.save规则满足时会触发bgsave

2.执行flushall命令后会触发

3.退出redis时会执行save操作 会触发快照操作

> 恢复rbb文件数据

只需要将rdb文件放在redis启动目录就可以 redis会自动恢复

查看文件放置的位置

```bash
config get dir
```

> 手动快照

```bash
1.bgsave		#redis会调用fork子进程在后台进行快照 写 操作 此时父进程仍旧可以相应客户端请求
				#一开始父子进程共享相同内存（此时子进程可以拿到内存最大的资源 对硬盘进行写操作的速度很快） 					一但父进程进行了写操作 父子进程就会被剥离开 子进程用很小的一部分内存进行硬盘写操作
2.save		#在接收到save命令之后 redis将不会响应其他任何读写操作 直到快照创建完毕
```

==快照仍旧会不定项的丢失一定的数据，刚刚做完快照还没到下一次任何一个save选项条件时，此时如果redis服务宕机 会丢失刚刚快照之后的写操作==

## 十六.只追加日志文件（AOF）

> redis会将接收所有客户端的写操作以命令的方式记录到AOF文件中

### 1.开启AOF

```bash
appendonly on 改为 yes
```

### 2.同步频率

(1).always   将每一个写操作立即同步到文件中 可以保证redis不丢失任何数据	但是严重降低了redis速度

而且由于机械速度是有上限的 当请求过多 会达到速度上线 造成写入放大问题

如果是SSD 速度虽然很高 但是SSD受不了大量的小命令频繁的写入 使SSD寿命降低  

(2).everysec	每秒同步一次 当一秒内有大量的写操作 只会执行一次同步 虽然也会丢失数据 

==使用该机制和不适用任何持久化机制时性能相差无几 官方推荐==

(3).no	完全由操作系统决定是否同步 可能立刻可能永不 完全不推荐

### 3.AOF文件重写（压缩）

> 随着时间的推移 AOF文件会越来越大 很多命令都是过时的  极端的情况下会占用整个磁盘

重写机制不是对原来的文件进行压缩 而是生成新的文件替换掉原来的文件

==重写方式==

（1）.bgrewriteaof命令

收到此命令 redis将使用与快照类似的方式将内存中的数据以命令的方式保存到文件中 替换掉原来的文件

首先redis调用fork子进程 对内存中的数据进行快照操作 根据快照向临时文件以命令的方式写入 

此时父进程继续处理请求 并把新收到的写操作写到原来的AOF文件中 并把这些命令缓存起来

==这样做的原因是因为如果子进程操作失败 不至于后收到的命令丢失== 

当子进程重写AOF文件成功后 会把缓存的新命令加入到新的AOF文件中 并替换掉老AOF文件

（2）. 

![image-20210303204710629](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210303204710629.png)

两种持久化策略可以一起使用 但是启动时优先使用AOF加载数据

快照持久化其实已经可以满足大部分的应用程序

## 十七.主从复制读写分离

> 最少为一主 二从   保证主机宕机后的选举机制

```bash
info replication		#查看当前库的信息
>role:master	#角色 默认情况下每一台redis服务都是主节点
>connected_slaves 0		#没有从机
```

配置文件

![image-20210303212338434](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210303212338434.png)

==只配置从库 不配置主库== 

使用命令完成主从

```bash
slaveof  ip 端口
```

使用配置文件配置

replicaof ip 端口

> 主机负责写 从机负责读 主机中所有的数据都会被从机自动保存

如果此时主机宕机  由于没有配置哨兵，并不会从新选择主机，但从机仍旧可以查 主机恢复时 所有功能恢复

如果此时从机宕机，重新恢复后如果是配置文件配置 那么一切正常 如果是命令完成 将会失去主机 需要重新配置

> 复制原理

![image-20210303220017822](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210303220017822.png)