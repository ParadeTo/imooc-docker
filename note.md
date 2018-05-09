# Install
## vagrant

```
vagrant init centos/7
vagrant up
vagrant ssh

vagrant status

vagrant halt

vagrant destroy
```

## install docker in CentOS7
```
systemctl start docker
```

## docker machine
可以通过下面这个命令在 virtualbox 里面安装一台迷你虚拟机，类似 vagrant
```
docker-machine create demo
```

## user

```
sudo groupadd docker
sudo gpasswd -a vagrant docker
```

其他操作

```
docker-machine ls
docker-machine ssh name
```

docker machine 作为宿主机的 server

1. 停掉宿主机的 server
2. 启动一台 docker-machine
3. eval $(docker-machine env name)

## docker machine + aliyun driver


## docker playground


# 介绍
## docker engine
* 后台进程
* rest api server
* cli 接口

## 底层技术
* namespace：命名空间
* countrol groups：资源控制
* union file systems：container 和 image 的分层

## image
* 文件和 meta data 的集合
* 分层的，并且每一层都可以添加改变删除文件，成为一个新的 image
* 不同的 image 可以共享相同的 layer
* image 本身是 read-only 的

### image 的获取
#### Build from Dockerfile

```
docker build -t youxingzhi/redis:latest .
```

#### Pull from Registry

```
docker pull ubuntu:14.04
```


### DIY 一个 Base Image
1. 用 go 语言编写一个 hello world 的程序并编译成二进制文件

2. Dockerfile

```
FROM scratch
ADD hello /
CMD ["/hello"]
``` 

3. build

```
docker build -t youxingzhi/hello-world .
```

4. inspect image

```
docker history bbc634
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
bbc634a2ec72        8 minutes ago       /bin/sh -c #(nop)  CMD ["/hello"]               0B
fc40cd789f84        8 minutes ago       /bin/sh -c #(nop) ADD file:7b052a90fd97bc54f…   2.1MB
```

## container
* 通过 image 创建
* 在 image layer 之上建立一个 container layer （可读写）
* 类比面向对象：类和实例
* image 负责 app 的存储和分发，container 负责运行


## Dockerfile
### FROM
```
FROM scratch # 制作 base image
FROM centos # 使用 base image
FROM ubuntu:14.04
```

### LABEL
定义 image 的 metadata

### RUN
每运行一个都会新生成一层，为了避免无用分层，可以合并多条命令成一行
```
RUN yum update && yum install -y vim \
	yum install -y nginx
```

### WORKDIR
设置工作目录，如果没有会新创建目录
```
WORKDIR /test
WORKDIR demo
RUN pwd # 输出结果应该是 /test/demo
```

### ADD and COPY
`ADD/COPY 宿主机 容器`

区别 1：
ADD 可解压缩


###  ENV
```
ENV MYSQL_VERSION 5.6 
RUN apt-get install -y mysql-server="${MYSQL_VERSION}" 
```

### RUN vs CMD vs ENTRYPOINT
RUN 

执行命令并创建新的 image layer

CMD 

设置容器启动后默认执行的命令和参数

如果 docker run 指定了其他命令， CMD 命令会被忽略

多个 CMD ，只有最后一个执行
	
ENTRYPOINT 

设置容器启动时运行的命令

不会被忽略

两种格式：
* shell 格式
可以访问 ENV 定义的变量 $name
```
ENTRYPOINT echo "hello $name"
```

* exec 格式
```
ENTRYPOINT ["echo", "hello $name"]
```

如果需要访问定义的变量，则用下面的方法：

```
ENTRYPOINT ["/bin/bash", "-c", "echo hello $name"]
```





