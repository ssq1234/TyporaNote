> # **  Linux相关**

## 一.**Linux常用命令**

### 1.查看有关Java的进程

```shell
ps -ef | grep java
```

### 2.强迫进程立即停止（-9）

```shell
kill -9 [PID]
```

### 3.不挂断运行命令,当账户退出或终端关闭时,程序仍然运行：

```shell
nohup java -jar XXX.jar &
```

> （承上）列出所有后台作业：jobs
>
> （承上）把作业调回前台：fg 编号
>

### 4.查看防火墙状态

```shell
 service iptables status
```

### 5.关闭防火墙 

```shell
service iptables stop
```

### 6.监听日志

```shell
tail -f 日志文件
```

### 7.查看储存空间

```shell
df -hl
```

### 8.查看文件

```shell
cat 文件名
```



### **4.设置开机启动**

```shell
systemctl enable mongod.service
```

### **5.启动Mongo shell**

```shell
命令：mongo 
```

## 三.配置下载源为aliyun

### 1.进入/etc/yum.repos.d目录

```powershell
cd /etc/yum.repos.d
```

### 2.CentOS-Base.repo改名

> 默认使用的是CentOS-Base.repo，所以我们需要对默认的文件改名

```powershell
mv CentOS-Base.repo CentOS-Base.repo.back
```

### 3.下载repo文件

```powershell
wget http://mirrors.aliyun.com/repo/Centos-7.repo
```

### 4.Centos-7.repo改名

> 将下载的国内源改名为默认的文件名

```powershell
mv Centos-7.repo CentOS-Base.repo
```

### 5.执行yum源更新命令

```powershell
yum clean all 
yum makecache 
12
```

### 6.查看yum源

```powershell
yum repolist all
```



