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



## 二.MongoDb命令

> Linux Centos 使用yum安装MongoDB 4.0
>

### **1.配置MongoDB的yum源**

> 创建yum源文件：
>

```shell
cd /etc/yum.repos.d 

vim mongodb-org-4.0.repo 
```

> 添加以下内容：（我们这里使用阿里云的源）
>

```markdown
[mongodb-org] name=MongoDB Repository baseurl=http://mirrors.aliyun.com/mongodb/yum/redhat/7Server/mongodb-org/4.0/x86_64/ gpgcheck=0 enabled=1
```

> 这里可以修改 gpgcheck=0, 省去gpg验证
>

> 安装之前先更新所有包 ：
>

```shell
yum update
```

### **2.安装MongoDB**

> 安装命令：
>

```shell
yum -y install mongodb-org
```

> 安装完成后
>
> 查看mongo安装位置

```shell
 whereis mongod
```

> 查看修改配置文件  

```shell
vim /etc/mongod.conf
```

>  bindIp: 172.0.0.1 改为 bindIp: 0.0.0.0
>
> （注意冒号与ip之间需要一个空格）
>

### **3.启动MongoDB** 

```shell
启动mongodb ：sudo systemctl start mongod.service

停止mongodb ：sudo systemctl stop mongod.service

查到mongodb的状态：systemctl status mongod.service
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



