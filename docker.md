
## docker使用教程

### 两个概念
docker容器

docker镜像

### docker安装本地镜像

docker build -t docker-image-demo:1.0.0 .

### docker安装远程镜像

### 启动docker镜像

docker run -d docker-image-demo

#### 宿主机器链接容器
docker run -p 127.0.0.1:2379:2379 -d etcd 

### 进入docker镜像中用shell进行交互

docker exec -it docker_name /bin/sh

exit退出交互

### 查看docker容器的ip

sudo docker inspect -f='{{.NetworkSettings.IPAddress}}' $(sudo docker ps -a -q)


### 多个docker和多个机器的连接


### Dockerfile

FROM,RUN,ENV,COPY,EXPOSE,ENTRYPOINT

### .dockerignore文件