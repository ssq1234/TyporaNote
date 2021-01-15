> # **Linux相关**

**Linux常用命令**

1.查看有关Java的进程：ps -ef | grep java

2.强迫进程立即停止（-9）：kill -9 [PID]

3.不挂断运行命令,当账户退出或终端关闭时,程序仍然运行：nohup java -jar XXX.jar &

（承上）列出所有后台作业：jobs

（承上）把作业调回前台：fg 编号

4.查看防火墙状态 service iptables status

关闭防火墙 service iptables stop

**MongoDb命令**

Linux Centos 使用yum安装MongoDB 4.0

**1.配置MongoDB的yum源**

创建yum源文件：

\#cd /etc/yum.repos.d 

\#vim mongodb-org-4.0.repo 

添加以下内容：（我们这里使用阿里云的源）

[mongodb-org] name=MongoDB Repository baseurl=http://mirrors.aliyun.com/mongodb/yum/redhat/7Server/mongodb-org/4.0/x86_64/ gpgcheck=0 enabled=1

这里可以修改 gpgcheck=0, 省去gpg验证

安装之前先更新所有包 ：

\# yum update

**2.安装MongoDB**

安装命令：

yum -y install mongodb-org

安装完成后

查看mongo安装位置 whereis mongod

查看修改配置文件 ： vim /etc/mongod.conf

 bindIp: 172.0.0.1 改为 bindIp: 0.0.0.0

（注意冒号与ip之间需要一个空格）

**3.启动MongoDB** 

启动mongodb ：sudo systemctl start mongod.service

停止mongodb ：sudo systemctl stop mongod.service

查到mongodb的状态：systemctl status mongod.service

**4.设置开机启动**

systemctl enable mongod.service

**5.启动Mongo shell**

命令：mongo 