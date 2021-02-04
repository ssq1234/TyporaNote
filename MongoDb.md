> # **MongoDb相关**

## 一.MongoDb命令

> Linux Centos 使用yum安装MongoDB 4.0

### **1.配置MongoDB的yum源**

> 创建yum源文件：

```shell
cd /etc/yum.repos.d 

vim mongodb-org-4.0.repo 
```

> 添加以下内容：（我们这里使用阿里云的源）

```markdown
[mongodb-org] name=MongoDB Repository baseurl=http://mirrors.aliyun.com/mongodb/yum/redhat/7Server/mongodb-org/4.0/x86_64/ gpgcheck=0 enabled=1
```

> 这里可以修改 gpgcheck=0, 省去gpg验证

> 安装之前先更新所有包 ：

```shell
yum update
```

### **2.安装MongoDB**

> 安装命令：

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
>  （注意冒号与ip之间需要一个空格）

### **3.启动MongoDB** 

```shell
启动mongodb ：sudo systemctl start mongod.service

停止mongodb ：sudo systemctl stop mongod.service

查到mongodb的状态：systemctl status mongod.service
```

### 

## 二.**指令**

![img](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/clipboard-1610699168973.png)

![img](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/clipboard-1610699178541.png)

![img](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/clipboard-1610699183549.png)

![img](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/clipboard-1610699188281.png)

![img](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/clipboard-1610699192892.png)