# Docker

## 一.Centos7安装

> 下载安装

```shell
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```

> 启动、停止、重启、设置为开机自启动、服务状态

```shell
systemctl start docker
systemctl stop docker
systemctl restart docker
systemctl enable docker
systemctl status docker
```

> 配置镜像加速

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://3uo8pnez.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 二.docker镜像操作

```shell
docker info
docker version	查看版本
docker --help   查看帮助
docker images	查看有哪些镜像
docker images -q	查看有哪些镜像（只显示镜像id）
docker pull		下载镜像 访问docker hub
docker search	寻找镜像 不建议
docker image rm 名称|ID	删除镜像	docker rmi 是 docker image rm简写
docker image rm -f 名称|ID	强制删除
docker rmi -f $(docker images -q)	把检索镜像结果全删除
docker images 名称	检索镜像
```

## 三.docker容器操作

```shell
docker load -i 文件名 //将镜像导入docker本地仓库
docker ps //查看当前运行的容器
docker ps -a//查看运行的容器和非运行的容器
docker ps -q //返回正在运行的容器id
docker ps -qa //返回正在运行的容器和非运行的容器id
```

![image-20210202195334958](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210202195334958.png)

```shell
docker run -p 8080:8080 ID|NAME //映射宿主机端口启动   -p可以多个映射多个端口
docker run -d -p 8080:8080 ID|NAME //映射宿主机端口启动 并后台运行
docker run -d -p 8080:8080 ID|NAME --name Name 起个名字//映射宿主机端口启动 并后台运行 起个名字
docker start ID|NAME
docker restart ID|NAME
docker stop  ID|NAME
docker kill  ID|NAME//启动 重启 停止 杀死
```

```shell
docker rm  ID|NAME //删除未启动的容器  
docker rm -f ID|NAME//删除正在运行的容器
docker rm -f $(docker ps -qa)  //删除所有容器
```

```shell
docker logs ID|NAME //查看日志
docker logs -f ID|NAME //查看实时日志
docker logs -tf ID|NAME //查看实时日志 并加入时间戳
docker logs --tail n //只显示日志后n行
```

```shell
docker top ID|NAME //查看容器内的进程
```

```shell
docker exec -it ID|NAME  bash  //进入容器内的终端 bash操作命令
exit //退出容器
```

```shell
docker cp ID|NAME:容器内资源路径  操作系统路径    //从容器往外部拷贝
docker cp 容器内资源路径 ID|NAME:容器内资源路径	//向容器内拷贝
```

```shell
docker inspect   //查看容器内部状态
```

> 数据卷volume	可以实现宿主机和容器的文件共享 比如共享一个文件夹
>
> 对宿主机文件操作时	可以同时影响到容器
>
> **必须在容器启动之前绑定一个数据卷**
>
> 数据卷不会影响镜像
>
> 数据卷一直会存在 即使容器被删除

```shell
//当目录为空时 容器内被映射的文件夹内会被清空
docker run -d -p 8080:8080 ID|NAME --name Name -v 宿主机目录:容器目录 //自定义实现数据卷绑定
//加ro（readonly）只能通过宿主机改变容器，容器不能改变宿主机
docker run -d -p 8080:8080 ID|NAME --name Name -v 宿主机目录:容器目录:ro 
//当目录不存在时    docker会在自己的目录下自动创建一个目录做映射 并且把容器内文件夹内的内容复制进来
//自动创建的文件路径是 var/lib/docker/volumes/数据卷名称
docker run -d -p 8080:8080 ID|NAME --name Name -v 数据卷名字:容器目录 //自动实现数据卷绑定
docker volume ls //查看有哪些数据卷
docker volume inspect 数据卷名称 //查看数据卷细节
docker volume create 数据卷名称 //创建新的数据卷
docker volume prune //删除没有被用到的数据卷
docker volume rm 数据卷名称 //删除指定的数据卷
```

```shell
docker commit -m 描述 -a 作者 ID|NAME 新的镜像名称   //打包自己的镜像
docker save 镜像名称 -o 镜像名.tar  //将镜像保存到当前路径下
```

## 四.docker网络操作

```shell
docker network ls //查看有哪些网桥
docker network create NAME //创建一个网桥
//指定网桥启动 网桥名称与ip映射
docker run -d -p 8080:8080 ID|NAME --name Name 起个名字 --network 网桥名称
docker network rm NAME //删除网桥
docker inspect NAME //查看网桥详细
```

## 五.docker安装常用的服务

### 1.MySql

```shell
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=密码 -d mysql:tag
```



