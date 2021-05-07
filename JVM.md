# JVM

## 一.JVM整体结构

![image-20210209174059355](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210209174059355.png)

> Java使用栈的优缺点

![image-20210209175241010](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210209175241010.png)

## 二.JVM生命周期

![image-20210209180253453](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210209180253453.png)

![image-20210209180644227](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210209180644227.png) 

## 三.JVM发展历程

### 1.Sun Classic VM

> JAVA最初的虚拟机

> HotSpot内置了该虚拟机  JDK1.4版本时被弃用

> 只提供了解释器 **没有提供即时编译器JIT** 但是可以外挂JIT 但是二者不能协同工作

> 外挂JIT后会使用JIT做为编译器 

> 单纯的使用JIT  会把每一行代码翻译成本地机器码 导致暂停时间过长 程序在启动时会有一段时间的卡顿

> 而二者如果可以协同工作 可以理解为一段路程  
>
> 1.我先用解释器 逐行翻译执行 **走两步** 等JIT翻译完毕我在上JIT这辆**公交车**
>
> 2.下了公交车我再用解释器  **走两步**  等JIT翻译完毕我在上另一辆JIT**公交车**

### 2.Exact VM （准确的VM）

> 准确式内存管理虚拟机

> 虚拟机可以知道内存中某个位置的数据具体是什么类型

> JDK1.2时提供了该虚拟机 但并没有大规模使用

> 具有现代虚拟机的
>
> **1.热点探测**（热点代码交给JIT）
>
> **2.编译器和解释器混合使用工作模式**

后来被HotSpot替换

### 3.HotSpot VM（热点代码探测技术）

> 三大商用虚拟机之一

> 从JDK1.3开始 HotSpot就成为了默认的虚拟机

> 提供了 **方法区**

![image-20210215131418775](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210215131418775.png)

### 4.JRockit

> 三大商用虚拟机之一 专注于服务器端应用 世界上最快的虚拟机

> 不包含解释器  所有的代码全部依靠JIT 因为服务端不需要太高的启动速度

> 后来甲骨文把JRockit整合到了HotSpot 但是由于架构相差很大 所以能整合的有限

### 5.J9

>三大商用虚拟机之一 专注于服务器端应用 世界上最快的虚拟机

> 市场定位与HotSpot接近 号称最快的虚拟机  实际速度低于JRockit

### 6.等等...

## 四.类加载子系统

> 负责从文件系统或者网络中以二进制流的方式加载.class文件 class文件在文件开头有特定的文件标识

> 只负责class文件的加载 至于能否正常运行 取决于执行引擎

加载的类信息被存到了**方法区** **方法区**内还会存储运行时常量池

### 1.Loading（加载）

(1).通过一个类的全限定名来获取定义此类的二进制字节流

(2).将字节流所代表的静态储存结构转化为方法区的运行时数据结构

(3).在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据访问入口

### 2.Linking（链接）

![image-20210215142257883](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210215142257883.png)

#### (1).验证

验证字节码文件是否合法

#### (2).准备

把类当中的静态变量赋值为默认值（零值） **final是例外的** final修饰的静态变量会在编译时赋值

#### (2).解析

类当中用到的打印输出等 转换为直接引用

### 3.Initialization（初始化）

![image-20210215143320986](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210215143320986.png)

#### (1).clinit方法会把类变量初始化和静态代码块初始化合并到一起 如下

```java
private static int a = 1;
static(){
    a = 2;
}
 // 此时启动代码a=2 
```

> 在执行子类的clinit方法前 要保证父类clinit已经执行完毕

#### (2).顺序执行

```java
static(){
    number = 20;
    // sout(number)  这是错误的 非法的前向引用
}
private static int number = 10;
 // 此时number = 10；
 // 原因是因为顺序执行 静态代码块在上 最终被覆盖掉了
```

#### (3).init方法

就是构造器方法

### 4.类加载器分类

![image-20210215150840106](JVM.assets/image-20210215150840106.png)

BootStrap Class Loader 是由C C++编写的

User_Defined Class Loader 是由Java语言实现的

```java
/**
 * 获取加载器
 * @author 孙术强
 * @date 2021/2/15 15:11
 */
public class Test {
    public static void main(String[] args) {
         // 获取系统类加载器
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.out.println(systemClassLoader);

         // 获取其上层：扩展类加载器
        ClassLoader extClassLoader = systemClassLoader.getParent();
        System.out.println(extClassLoader);

         // 获取其上层  获取不到！引导类加载器
        ClassLoader bootstrapClassLoader = extClassLoader.getParent();
        System.out.println(bootstrapClassLoader);

         // 对于用户自定义类来说 发现使用的是系统类加载器
        ClassLoader classLoader = Test.class.getClassLoader();
        System.out.println(classLoader);

        // 系统核心类库使用的是 引导类加载器
        ClassLoader classLoader1 = String.class.getClassLoader();
        System.out.println(classLoader1);
    }
}
```

#### (1).BootStrap 启动类加载器/引导类加载器

> 由C/C++实现 嵌套在JVM内部
>
> ==用来加载JAVA的核心类库==
>
> 并不继承至ClassLoader 没有父加载器
>
> 事扩展类加载器和应用程序加载器的父类加载器

#### (2).Extension 扩展类加载器

> 由Java编写 继承于ClassLoader
>
> 自定义的Jar放在jre/lib/ext子目录下 也会由扩展类加载器加载

#### (3).AppClassLoader 应用程序类加载器 系统类加载器

> 由Java编写 继承于ClassLoader
>
> 自己写的会由应用程序类加载器加载

#### (4).自定义类加载器

![image-20210217173129043](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210217173129043.png)

### 5.ClassLoader

> ClassLoader是一个抽象类 除了bootStrap类加载器 所有的类加载器都是直接继承或者间接继承自ClassLoader

### 6.双亲委派机制

> Java虚拟机对class文件采用的是按需加载模式  当需要使用到该类时才会进行加载
>
> 而加载类的方式就是双亲委派模式 即把请求交给父类处理
>
> 1.避免类的重复加载
>
> 2.保护核心API

![image-20210217174835280](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210217174835280.png)

==**反向委托**==

> 现在要加载一个第三方的jar包 例如jdbc.jar 需要用到引导类加载器加载核心api加载rt.jar 也就是加载第三方jar包实现的接口
>
> 而具体的实现类jdbc就需要引导类加载器反向委托给系统类加载器

### 7.类的主动使用和被动使用

![image-20210217181350775](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210217181350775.png)

### 8.其他

JVM判断两个类是不是同一个 

1.首先类名和包名是一致的

2.类加载器必须是一个

在方法区内记录了类的加载器类型

当解析一个类型到另一个类型的引用时，JVM需要保证两个类的类加载器是相同的

## 五.运行时数据区

![image-20210220134724651](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210220134724651.png)



![image-20210220134927884](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210220134927884.png)

> 一个线程拥有一份（程序计数器、本地方法栈、虚拟机栈） 而（方法区、堆）是线程共享的

==百分之95的垃圾来自堆区 百分之五的垃圾来自方法区==

Java虚拟机的线程和操作系统的线程是一一对应，是映射关系

### 1.JVM系统线程

![image-20210220140240763](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210220140240763.png)

### 2.程序计数器、PC寄存器

> PC寄存器用来储存指向下一条指令的地址，即将要执行的指令代码。由执行引擎读取下一条指令

> PC寄存器是一块很小的内存空间，几乎可以忽略不计也是最快的内存空间，他是每一条线程私有的

> 如果是在执行本地方法、那么就是未指定的值

> 循环、分支、跳转、异常处理、线程恢复都需要PC寄存器

> 此处是不会发生异常（OOM）的！

![image-20210220142656643](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210220142656643.png)

==为什么使用PC寄存器，让执行引擎自己记录执行位置不可以吗？==

> 当多线程时，多个线程执行位置各不相同，这时候每个线程都配备了一个PC寄存器、就能记录每一个线程的执行位置

==并行==：多个核来执行多个线程、多个线程是一起走的

==并发==：一个核通过上下文切换执行多个线程

### 3.虚拟机栈

> 虚拟机栈是线程私有的 内部保存着一个个的栈帧 （对应着一个个的方法）它的生命周期和线程的生命周期是一致的

==访问速度仅次于程序计数器，对于栈来说没有垃圾回收，但会抛出异常==

· 虚拟机栈容量不足时会抛出StackOverflowError异常

· 如果虚拟机栈可以动态扩展 如果内存不足 会抛出OutofMemoryError异常

```
-Xss size  设置虚拟机栈容量大小（参数调优）
```

Sets the thread stack size (in bytes). Append the letter `k` or `K` to indicate KB, `m` or `M` to indicate MB, and `g` or `G` to indicate GB. The default value depends on the platform:

- Linux/x64 (64-bit): 1024 KB
- macOS (64-bit): 1024 KB
- Oracle Solaris/x64 (64-bit): 1024 KB
- Windows: The default value depends on virtual memory

The following examples set the thread stack size to 1024 KB in different units:

```
Copy-Xss1m
-Xss1024k
-Xss1048576
```

This option is similar to `-XX:ThreadStackSize`.

==栈帧的内部结构==

> 栈帧内部结构由五部分组成

1.局部变量表

2.操作数栈（表达式栈）

3.动态链接（指向运行时常量池的方法引用）

4.方法返回地址（方法正常退出或者异常退出的定义）

5.一些附加信息

> 1和2主要影响栈帧的大小 而栈帧大小又会影响到栈能存下多少个栈帧 栈空间不足时 会抛出  stackoverflow1.

#### (1).局部变量表（局部变量数组或本地变量表）

> 定义为一个==***数字***==一维数组 存储方法参数和定义在方法内的局部变量
>
> > 这些数据类型包括**基本数据类型**（八种基本数据类型本质上都可以用数字进行标识，例如char类型可以使用ASCII码）、**对象引用**（16进制的地址 也是数字）、以及**returnAddress**

局部变量表所需要的容量大小是在编译期确定下来的（存在maximum local variables中） 在方法运行期间是不会修改大小的。

随着方法栈帧的销毁 局部变量表会随之销毁（皮之不存，毛将焉附）

> 对于slot（局部变量表基本存储单元（变量槽））的理解

32位以内的类型（包括引用类型）只占一个槽   64位类型（long（8字节），double（8字节））占用两个槽

> 对于非静态方法（包括构造方法）来说 在局部变量表数组的第一个位置 0 处 会存放this变量（当前正在创建的变量）
>
> 此处可以解释为什么静态方法当中不能够使用this？因为静态方法的局部变量表中不存在this变量

如果局部变量过了其作用域 slot则会被回收 再次定义局部变量时会重复利用被回收的slot

==变量的分类==

按照数据类型分：1.基本数据类型 2.引用数据类型

按照在类中声明的位置分：1.成员变量 ：在使用前都经历过默认初始化赋值

​																			类变量：linking的prepare阶段：给类变量默认赋值   inital阶段给类变量进行显式赋值（即静态代码块赋值（clint））

​																			实例变量：随着对象的创建会在堆空间中分配实例变量空间 并进行默认赋值

​															2.局部变量：在使用前是必须进行显式赋值 否则编译不通过

==虚拟机使用局部变量表实现方法的传递==

#### (2).操作数栈(表达式栈)

> 操作数栈底层数据结构是 数组实现的栈

主要用于保存计算过程的中间结果，同时作为计算过程中变量的临时的存储空间。

操作数栈就是JVM执行引擎的一个工作区，当一个方法开始执行时，随着栈帧的创建，一个空的操作数栈随之创建。

在编译期，操作数栈的大小就已经被定义好了。

32位的数据占一个深度，64位占两个深度。

操作数栈只能通过入栈出栈进行操作。

如果方法带有返回值，返回值也会被压入操作数栈中。

JAVA解释引擎是基于栈的执行引擎，栈指的就是操作数栈。

```java
int i = 1;
int j = 2;
int m = i + j;
```

1首先压栈进入操作数栈

1弹栈后进入局部变量表 i = 1

2首先压栈进入操作数栈

2弹栈后进入局部变量表 j = 2 

1，2从局部变量表接连压栈进入操作数栈

1，2接连弹栈进行求和操作存入局部变量表 m = 3     

#### (3).栈顶缓存技术

