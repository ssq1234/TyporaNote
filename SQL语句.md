> # **SQL语句相关**
>

## **1.查询常量值**

```mysql
SELECT 100；

SELECT "join";

SELECT 100*98;
```

## **2.查询函数**

```mysql
SELECT VERSION();  //查询版本
```



## **3.去重查询**

```mysql
SELECT distinct  ？from ？;
```



## **4.’+‘号作用**

如果数字+   则做加法运算

如果其中一方为字符型  会先尝试将字符转换为数字后进行加法

转换失败则字符默认为0

如果其中一方为NULL 则结果为NULL

## **5.CONCAT(字段,字段,......)**

进行字符串连接

## **6.IFNULL(字段，赋值)**

如果字段为NULL，则替换为值

## **7.like模糊查询**

%任意多个字符

_单个字符

转义字符自定义  

```mysql
like(_$_%)  ESCAPE '$';
```

将自定义字符后的一个字符当成实际字符而不是通配符

## **8.between and**

```mysql
between   120 and 100   //顺序不能反

==

<=120 and >=100
```



## **9.order by   (asc|desc)**

默认升序  

升序（从低到高）降序

## 10.LENGTH() 

## 获取长度 

## //字节长度  utf-8编码一中文三字节，GBK编码一字符两字节

**面试题

![yd14](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd14.png)

## **11.字符函数**

```mysql
upper（'value'） 变大写

lower （'value'）变小写

substr&&substring('value',指定索引) 截取从指定索引处后面所有字符

('value',指定索引,截取长度）截取从指定索引处截取指定长度

instr(字符,'指定字符') 查询指定字符第一次出现的索引，找不到返回0

trim('指定字符'， from ‘字符’) 将字符的前后所匹配到的指定字符删除

lpad（‘字符’，10，‘指定字符’）将字符向左填充指定字符至10

rpad （）呈上向右填充

replace（‘字符’，‘指定字符’，‘替换字符’）
```



## **12.数学函数**

```mysql
round() 四舍五入

round(1.56)    =   2

round(1.567,2)  小数点后保留两位

ceil() 向上取整，返回>=该参数的最小整数

floor() 向下取整,，返回<=该参数的最大整数

truncate() 截断

truncate(1.69999,1) = 1.6

mod(a,b) a除以b取余（a-a/b*b）
```



## **13.日期函数**

```mysql
now() 当前日期+时间（年-月-日 时-分-秒）

curdate() 返回当前系统系统日期，不包含时间

curtime() 返回当前系统时间，不包含日期

获取指定部分

year(now()) year('')

month(now()) month('')

monthname  返回月份英文名称
```

![yd15](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/yd15.png)

```mysql
str_to_date 将字符通过指定格式转换为日期

str_to_date('1998-3-2','%Y-%c-%d')

date_format 将日期转换为字符

date_format(now(),'%y年%m月%d日')
```



## **14.其他函数**

```mysql
version() 当前版本

database() 当前数据库

user() 当前用户
```



## **15.流程控制函数(if else 效果)**

```mysql
if(10>5,'大','小')  三目

case()
```



### 一.switch case效果

```mysql
case  要判断的字段或表达式

when 常量1 then 要显示的值1或语句1；（值不加分号，语句加）

when 常量2 then 要显示的值2或语句2；

。。

else 要显示的值n或语句n

end
```



### 二.类似多重if

```mysql
case

when 条件1 then 要显示的值1或语句1；（值不加分号，语句加）

when 条件2 then 要显示的值2或语句2；

。。。

else 要显示的值n或语句n

end
```



## 16.分组函数

```mysql
distinct 去重

count（distinct，   ）等

sum avg max min count 自动忽略null值

datedief（日期，日期）  前面日期减后面日期
```



## 17.分组查询

