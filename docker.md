# 1.安装jdk1.8(centos7.5)

**yum安装**

1.搜索jdk安装包

```
yum search java|grep jdk
```

2.下载jdk1.8，下载之后默认的目录为： /usr/lib/jvm/

```
yum install java-1.8.0-openjdk
```

3配置环境变量及验证安装

```
java -version
```

# 2.安装docker

1.初始化环境，卸载旧的版本

```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2.需要的安装包

```
yum install -y yum-utils
```

3.设置镜像仓库

```
 yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

4.安装docker

```
yum install docker-ce docker-ce-cli containerd.io
```

5.启动docker

```
systemctl start docker
```

6.是否安装成功

```
docker version
```

卸载

```
yum remove docker-ce docker-ce-cli containerd.io


rm -rf /var/lib/docker
```

7.配置阿里镜像加速

![58986259450](C:\Users\Administrator\Desktop\安装\arrest\1589862594508.png)

```
sudo mkdir -p /etc/docker


sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://ui6jarkn.mirror.aliyuncs.com"]
}
EOF


sudo systemctl daemon-reload


sudo systemctl restart docker
```

# 3.docker命令

## 3.1docker帮助命令

```
docker version

docker info

docker 命令 --help  比如docker images --help
```

## 3.2docker 镜像命令

```
docker images -a 查看所有镜像

docker search [] 搜索镜像 ：docker search mysql

docker pull  [] 下载镜像

docker rmi [] 删除镜像

docker rmi -f $(docker images -aq) 删除所有镜像
```

## 3.3docker 容器命令

```shell
docker run [可选参数] images

# 参数说明

--name=      容器的名称
-d           后台方式运行
-it          使用交互式方式运行，进如容器查看
-p           指定主机端口与容器端口映射 8080：8080


docker stats 查看容器运行状态
# 测试

docker run -it mysql /bin/bash 启动并进如容器 
```

```shell

#退出容器，容器停止
exit 

ctrl + q +p 退出容器，容器不停止

#查看容器
docker ps 查看运行中的容器 
          -a 查看所有容器
#删除容器          
docker rm [容器id] 删除容器，不能删除运行中的容器
		-f [容器id] 强制删除运行中的容器
#启动和停止rq
docker start [容器id] 启动容器

docker restart [容器id] 重启容器

docker stop [容器id] 停止容器

docker kill [容器id] 强制停止容器




```

## 3.4其他命令

### 3.4.1后台启动命令

![58986565944](C:\Users\Administrator\Desktop\安装\arrest\1589865659440.png)

### 3.4.2查看日志

![58986587125](C:\Users\Administrator\Desktop\安装\arrest\1589865871255.png)

### 3.4.3查看容器进程信息

![58986595678](C:\Users\Administrator\Desktop\安装\arrest\1589865956783.png)

### 3.4.5查看镜像元数据

![58986606347](C:\Users\Administrator\Desktop\安装\arrest\1589866063477.png)

### 3.4.6进如当前正在 运行的容器（重要）

![58986618817](C:\Users\Administrator\Desktop\安装\arrest\1589866188170.png)



### 3.4.7从容器内拷贝文件到主机

![58986648644](C:\Users\Administrator\Desktop\安装\arrest\1589866486446.png)

### 3.4.8命令图

![58986661789](C:\Users\Administrator\Desktop\安装\arrest\1589866617891.png)

# 4.可视化

portainer

![58987114460](C:\Users\Administrator\Desktop\安装\arrest\1589871144600.png)

```
docker run -d -p 9000:9000 --restart always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问www.allweing.cn:9000

![58987159849](C:\Users\Administrator\Desktop\安装\arrest\1589871598497.png)

![58987163732](C:\Users\Administrator\Desktop\安装\arrest\1589871637326.png)

![58987167709](C:\Users\Administrator\Desktop\安装\arrest\1589871677090.png)

# 5,提交一个自己的镜像

![58987293377](C:\Users\Administrator\Desktop\安装\arrest\1589872933776.png)



提交一个容器为镜像

```
docker commit -m="my first tomcat" -a="zzzgy" 55c4d304adfc mytomcat:1.0
               #信息                 #作者     #容器id      镜像名：版本号
```

![58987329035](C:\Users\Administrator\Desktop\安装\arrest\1589873290355.png)

# 6.容器数据卷

容器的持久化和同步操作，容器间数据共享

```
docker run -d --name zhutomcat -p 8081:8080 -v /home/webapps:/usr/local/tomcat/webapps mytomcat:1.0

```

**通过文件挂载，就可以在主机上修改文件内容同步到容器中**

![58987635831](C:\Users\Administrator\Desktop\安装\arrest\1589876358313.png)

![58987638733](C:\Users\Administrator\Desktop\安装\arrest\1589876387332.png)

# 7.dockerfile(命令参数脚本)

## 7.1dockerfile构建过程

![58988202256](C:\Users\Administrator\Desktop\安装\arrest\1589882022563.png)

## 7.2dockerfile 命令

![58988242302](C:\Users\Administrator\Desktop\安装\arrest\1589882423025.png)

```
docker history [镜像id] 查看构建历史
```

##实战：自己做一个centos镜像

1. cd /home

2. mkdir dockerfile

3. cd dockerfile

4. vim mydockerfile

5. ```shell
   FROM centos
   MAINTAINER zzzgy<1589953744@qq.com>

   ENV MYPATH /usr/local
   WORKDIR $MYPATH

   RUN yum -y install vim
   RUN yum -y install net-tools

   EXPOSE 80

   CMD echo $MYPATH
   CMD echo "------------end--------------"
   CMD  /bin/bash

   ```

6. ```shell
    docker build -f mydockerfile -t mycentos:0.1 .
    ```
   ```

7. ```
   Successfully built 557aca4492da
   Successfully tagged mycentos:0.1
   #成功
   ```

```
docker history [镜像id] 查看构建历史
```

## 7.3cmd 与ENTORYPOINT区别

![58988511823](C:\Users\Administrator\Desktop\安装\arrest\1589885118237.png)

ENTORYPOINT 可以直接追加命令

## 实战：构建一个tomcat镜像

![58988974820](C:\Users\Administrator\Desktop\安装\arrest\1589889748204.png)

```shell
docker build -t mytomcat .
#如果文件名是dockerfile 就不用指定文件
docker build -f mydockerfile -t mycentos .
```

## 7.4发布镜像

![58989079734](C:\Users\Administrator\Desktop\安装\arrest\1589890797345.png)

![58989096609](C:\Users\Administrator\Desktop\安装\arrest\1589890966095.png)

![58989132111](C:\Users\Administrator\Desktop\安装\arrest\1589891321111.png)

参考阿里云文档

小结

![58989144773](C:\Users\Administrator\Desktop\安装\arrest\1589891447734.png)

# 8docker网络

![58989168958](C:\Users\Administrator\Desktop\安装\arrest\1589891689584.png)



![58989239761](C:\Users\Administrator\Desktop\安装\arrest\1589892397617.png)

![58989272054](C:\Users\Administrator\Desktop\安装\arrest\1589892720548.png)

# 9springboot微服务打包成docker



1.构建springboot项目

2.打包应用

3.编写dockerfile

4.构建镜像

5.发布运行



![58989461474](C:\Users\Administrator\Desktop\安装\arrest\1589894614747.png)































































# 实战部署

## 1.redis集群部署

![58989354106](C:\Users\Administrator\Desktop\安装\arrest\1589893541067.png)

![58989370403](C:\Users\Administrator\Desktop\安装\arrest\1589893704032.png)

## 2.mysql安装

```shell
docker pull mysql:5.7


docker run --name mysql57 -e MYSQL_ROOT_PASSWORD=root -d -p 3306:3306 mysql:5.7

```

## 3.tomcat 安装

```
docker pull tomcat:8.0.15


docker run -d -p 8080:8080 --name mytomcat -v /mytomcat/webapps:/usr/local/tomcat/webapps tomcat:8.0.1
```

```
cp -r webapps.dist/* webapps
```

## 4.nginx安装

```shell
#拉取镜像
docker pull nginx
#进入根目录，创建文件夹
cd /
mkdir -r /nginx/html
mkdir -r /nging/path

#创建临时nginx容器
docker run --name tmp-nginx-container -d nginx

#将容器内的nginx.conf复制到主机文件夹来
docker cp tmp-nginx-container:/etc/nginx/nginx.conf /nginx/path/nginx.conf

#删除临时nginx容器
docker rm -f tmp-nginx-container

#创建nginx容器，并挂载文件
docker run -d -p 80:80 --name mynginx -v /nginx/html:/usr/share/nginx/html -v /nginx/path/nginx.conf:/etc/nginx/nginx.conf nginx

#查看容器
docker ps 

```

## 5.redis 单机安装

```shell
docker pull redis

docker run -d -p 6379:6379 --name myredis redis
```

## 6.elasticsearch安装

```
docker pull elasticsearch：5.6


docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name myelasticsearch elasticsearch:5.6
```



## 7.RabbitMQ安装

```
#指定版本，该版本包含了web控制页面
docker pull rabbitmq:management

#方式一：默认guest 用户，密码也是 guest
docker run -d --hostname my-rabbit --name rabbit -p 15672:15672 -p 5672:5672 rabbitmq:management

#方式二：设置用户名和密码
docker run -d --hostname my-rabbit --name rabbit -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=password -p 15672:15672 -p 5672:5672 rabbitmq:management
```

