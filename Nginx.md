# Nginx

## 一.正向代理

> 在客户端（浏览器）配置代理服务器，通过代理服务器进行访问

![image-20210324192359019](Nginx.assets/image-20210324192359019.png)

## 二.反向代理

![image-20210324192913636](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210324192913636.png)



## 三.负载均衡

![image-20210324193716150](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210324193716150.png)

## 四.动静分离

![image-20210324194146119](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210324194146119.png)

## 五.Centos7安装

安装所需插件
1、安装gcc
gcc是linux下的编译器在此不多做解释，感兴趣的小伙伴可以去查一下相关资料，它可以编译 C,C++,Ada,Object C和Java等语言

命令：查看gcc版本 

gcc -v


一般阿里云的centOS7里面是都有的，没有安装的话会提示命令找不到，

安装命令:

yum -y install gcc


2、pcre、pcre-devel安装
pcre是一个perl库，包括perl兼容的正则表达式库，nginx的http模块使用pcre来解析正则表达式，所以需要安装pcre库。

安装命令：

yum install -y pcre pcre-devel


3、zlib安装
zlib库提供了很多种压缩和解压缩方式nginx使用zlib对http包的内容进行gzip，所以需要安装

安装命令：

yum install -y zlib zlib-devel


4、安装openssl
openssl是web安全通信的基石，没有openssl，可以说我们的信息都是在裸奔。。。。。。

安装命令：

yum install -y openssl openssl-devel


安装nginx
1、下载nginx安装包
wget http://nginx.org/download/nginx-1.9.9.tar.gz  


2、把压缩包解压到usr/local/java
tar -zxvf  nginx-1.9.9.tar.gz


3、切换到cd /usr/local/java/nginx-1.9.9/下面
执行三个命令：

./configure

make

make install


5、切换到/usr/local/nginx安装目录


6、配置nginx的配置文件nginx.conf文件，主要也就是端口


可以按照自己服务器的端口使用情况来进行配置

ESC键，wq！强制保存并退出

7、启动nginx服务
切换目录到/usr/local/nginx/sbin下面

启动nginx命令：

./nginx

8、查看nginx服务是否启动成功
ps -ef | grep nginx

## 六.常用命令

> 进入/usr/local/nginx/sbin    

### 1.查看nginx版本号

```bash
./nginx -v
```

### 2.关闭nginx

```bash
./nginx -s stop
```

### 3.启动nginx

```bash
./nginx
```

### 4.重启nginx（重加载）

> 对配置文件进行重新加载

```bash
./nginx -s reload
```

## 七.配置文件

```bash
 // 全局块
#user  nobody;
worker_processes  1;  //处理并发数量

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

 // events块
events {
    worker_connections  1024; // 最大连接数
}

 // http块 使用最频繁
http {
	 // http全局块
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
	 // server块 虚拟主机相关
    server {
        listen       8344;  //启动端口
        server_name  localhost; // 本地服务地址

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            proxy_pass  http://localhost:8080;  // 服务转发位置
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    server {
        listen       8000;
        server_name  localhost;

        location ~/edu/ {  //包含/edu/转发的位置
            proxy_pass  http://localhost:8080;  // 服务转发位置
        }
        
        location ~/wod/ {  //包含/wod/转发的位置
            proxy_pass  http://localhost:8081;  // 服务转发位置
        }
    }


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

## 八.正则表达式规则

![image-20210324204545507](https://typora1-1304288279.cos.ap-beijing.myqcloud.com/image-20210324204545507.png)