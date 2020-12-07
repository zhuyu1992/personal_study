### 一、docker容器技术入门

#### 1、容器化技术

主机虚拟化

- 共用一组硬件
- 独立内核

容器虚拟化

- 共用一组硬件
- 共用一个内核
- 独立UTS,Mount,IPC,PID,User,Net,namespaces
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559143055248.png)
- Control Group：分配容器资源

LXC：LinuX Container
docker --->LXC的增强版，简化LXC
docker优势：统一环境，一次开发多出运行
docker劣势：增加运维的难度，因为一个容器一个进程，要进行调试要突破边界，进入容器才能调试

#### 2、docker的安装

**安装一些必要的系统工具：**

```haskell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

**添加软件源信息：**

```vim
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

**更新 yum 缓存：**

```ebnf
sudo yum makecache fast
```

**安装 Docker-ce：**

```ebnf
sudo yum -y install docker-ce
```

**启动 Docker 后台服务**

```ebnf
sudo systemctl start docker
```

**测试运行 hello-world**

```elixir
[root@runoob ~]# docker run hello-world
```

由于本地没有hello-world这个镜像，所以会下载一个hello-world的镜像，并在容器内运行。

**镜像加速**

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，我们可以需要配置加速器来解决，我使用的是网易的镜像地址：http://hub-mirror.c.163.com。

新版的 Docker 使用 /etc/docker/daemon.json（Linux） 或者 %programdata%\docker\config\daemon.json（Windows） 来配置 Daemon。

请在该配置文件中加入（没有该文件的话，请先建一个）：

```json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

**查看docker版本**

```d
docker version
```

**查看docker中的镜像**

```ebnf
docker images
```

**下载安装centos虚拟镜像**
查看docker有哪些镜像

```stata
docker search centos
```

下载镜像

```nginx
docker pull docker.io/centos
```

进入docker虚拟镜像

```nginx
docker run -it docker.io/centos:latest /bin/bash
```

### 二、docker的基本用法

镜像：静态的
容器：动态的，有生命周期

#### 运行守护式容器

**方式一**
运行容器

```applescript
docker run -it centos /bin/bash
```

ctrl+p和ctrl+q退出容器，这个时候容器就在后台启动了
进入到运行中的容器：`docker attach 容器名`
**方式二**
使用run -d

```dockerfile
docker run --name dc1 -d centos /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

--name：给容器取个名字
-d：让容器在后台运行
-c：使容器执行sh脚本（打印好看日志）

#### 查看日志

```yaml
docker logs  [-f] [-t] [-tail] 容器名
-f  --follows=true | false默认为fase
-t --timestamps=true | false 默认 false
-tail = "all"
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559055642525.png)
查看容器中的进程

```nginx
docker top 容器名
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559055988821.png)

#### 执行新的线程

```inform7
docker exec [-d] [-i] [-t] 容器名
```

#### 停止容器

```livecodeserver
docker stop 容器名（发送一条命令给容器停止）
docker kill 容器名（直接停止）
```

### 三、docker 镜像管理基础

- docker镜像含有启动容器所需要的文件系统及内容，因此，其用于创建并启动docker容器
  - 采用分层构建机制，最底层为boofs，其之上为roofs
    - bootfs：用于系统引导的文件系统，包括bootloader和kernel，容器启动后会被卸载以节省内存资源；
    - roofs：位于bootfs之上，表现为docker容器的根文件系统
      - 传统模式中，系统启动之时，内核挂载rootfs时会首先将其挂载为“只读”模式，完整性自检完成后将其重现挂载为读写模式。
      - docker中，rootfs由内核挂载为“只读”模式，而后通过“联合挂载”技术额外挂载一个“可写层”
        ![enter description here](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559369540559.png)
- Docker Image Layer
  - 位于下层的镜像称为父镜像，最底层的称为基础镜像
  - 最上层为“可读写”层，其下的均为“只读”层
    ![enter description here](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559369529256.png)
- Docker Registry
  容器启动时，docker daemon会试图从本地获取相关的镜像；本地镜像不存在时，其将从Registry中下载该镜像并保存到本地；
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559370362678.png)
- Docker Registry 分类
  - Registry 用于保存docker镜像，包括镜像的层次结构和元数据
  - 用户可以自建Registry，也可以使用官方的Docker Hub
  - 分类：
    - Sponsor Registry：第三方的Registry，供客户和Docker社区使用
    - Mirror Registry：第三方的Registry，只让客户使用
    - Vcndor Registry：由发布Docker镜像的供应商提供的Registry
    - Private Registry：通过设有防火墙和额外的安全层的私有实体提供的Registry
- Registry(repository and index)
- Repository
  - 由某特定的 dockerf镜像的所有迭代版本组成的镜像仓库
    - 一个 RegistryRepository
      - Repository可分为“顶层仓”和“用户仓库”
      - 用户仓库名称格式为“用户名/仓库名
    - 每个仓库可以包含多个ag(标签),每个标签对应一个镜像
  - Index
    - 维护用户帐户、镜像的校验以及公共命名空间的信息
    - 相当于为registry提供了一个完成用户认证等功能的检索接口

#### 制作镜像

##### 基于容器制作镜像

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559374836752.png)

- 进入交互式镜像

```applescript
 docker run --name b1 -it busybox
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559374862840.png)

- 创建一个目录和文件
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559375300008.png)
- 生成镜像文件

```stylus
docker commit -p b1
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559375407164.png)

- 查看生成的镜像文件
  此时的镜像文件只有id没有仓库名的标签名
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559375485797.png)
- 给镜像加上仓库名和标签名

```crmsh
docker tag 92cadba8bb5a erwin/httpd:v0.1
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559375640315.png)
​	一个镜像可以打多个标签

- 启动镜像查看文件是否存在

```applescript
 docker run --name t1 -it erwin/httpd:v0.1
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559376096212.png)

- 查看镜像启动命令

```stata
docker inspect b1
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559375902956.png)

- 制作镜像同时设置标签和修改启动命令

```scilab
docker commit -c 'CMD ["/bin/httpd","-f","-h","/data/html/"]' -p b1 erwin/httpd:v0.2
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559376424718.png)

- 启动镜像

```applescript
docker run --name t2 erwin/httpd:v0.2
```

 -访问服务
 ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559376865301.png)

##### 镜像的导入导出

- 导入

```stylus
docker save -o myimages.gz erwin/httpd:v0.2
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559377111820.png)

- 导出

```stylus
docker load -i myimages.gz
```

##### docker启动jar文件

- 下载java镜像

```css
	docker pull java:8u111
```

- 启动可执行文件

```stylus
docker run -d -p 9090:9090 -v /usr/springboot-1.jar:/usr/springboot-1.jar --name springboot java:8u111 java -jar /usr/springboot-1.jar
```

==-d 表示在后台启动
-p 9090:9090 表示将容器的端口 映射成宿主主机的端口，否则9090端口访问不到
-v /usr/springboot-1.jar:/usr/springboot-1.jar 表示将宿主主机的jar文件，映射到容器中（分号前为宿主主机的路径，分号后为容器中的路径）
--name springboot 表示为该容器取一个全局唯一的名称，这里我取的名称为springboot
java:8u111 表示镜像文件的名称和tag
java -jar /usr/springboot-1.jar 表示运行jar包，注意：这里的jar包为容器中的位置，是通过前面的-v属性映射的==

### 四、docker容器虚拟化网络

#### docker的四种网络类型

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559470576751.png)

- 封闭式网络
  创建封闭式网络的容器

```dsconfig
docker run --name t1 -it --network none --rm busybox:latest

--rm 表示退出即删除容器
--network 网络名称 创建容器并接入哪个网络
```

通过ifconfig查看网络
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559471137666.png)

- 桥接网络（默认）
  创建交接网路

```dsconfig
docker run --name t1 -it --network bridge --rm busybox:latest
```

查看网络
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559471352298.png)

- 容器联盟网络（共享容器net名称空间）
  创建容器

```dsconfig
docker run --name t1 --network container:t2 -it --rm busybox
创建容易并共享容器t2的网络名称空间
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559474321030.png)

- 主机联盟网络（共享主机net名称空间）

```dsconfig
docker run --name t1 --network host -it --rm busybox
创建并共享宿主机的网络名称空间
```

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559474583116.png)

#### docker容器的端口映射

- Docker为NAT桥,因此容器一般获得的是私有网络地址
- 可以把容器想像为宿主机NAT服务背后的主机
- 如果开放容器或其上的服务为外部网络访问,需要在宿主机上为其定义DNAT规则,例如
  - 对宿主机某IP地址的访问全部映射给某容器地址
    - 主机IP : : 容器IP
  - 对宿主机某IP地址的某端口的访问映射给某容器地址的某端口
    - 主机IP:PORT : : 容器IP:PORT
- 为docker run命令使用-p选项即可实现端口映射,无须手动添加规则
- -p选项的使用格式
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559472397894.png)
- **映射容器的80端口**

```dsconfig
docker run --name t1 --rm -p 80 erwin/httpd:v0.2
```

使用`iptables -t nat -vnL`查看映射的端口（或`docker ports 容器名`）
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559473048178.png)
自此可以通过访问宿主机的32768端口访问到容器的80端口
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559473114259.png)

#### 修改docker网桥地址

- 关闭docker

```vbscript
systemctl stop docker
```

- 修改/etc/docker/daemon.json

```json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"],
  "bip":"10.0.0.1/16"
}
```

- 查看网桥地址
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559475421173.png)

### 五、docker 存储卷

1. 什么是存储卷？容器与宿主机的文件关联。

2. 为什么使用存储卷？
   ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559514591718.png)

   ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559514746519.png)

3. 存储卷的类型
   ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559515031903.png)

4. 在容器中使用Volumes（卷）
   ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559515128962.png)
   **案例**

启动一个容器，使容器目录/data/t1挂载到主机/data/t1上

```jboss-cli
docker run --name t1 --rm -it -v /data/t1:/data/t1 busybox
```

在容器/data/t1中添加文件index.html
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559515622159.png)
查看宿主机的是否有这个文件
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559515667872.png)

1. 多容器共享存储卷
   ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559515810955.png)
   **使用--volumes-from**
   启动一个新的容器t2绑定t1的卷

```dsconfig
docker run --name t2 -it --volumes-from t1  busybox
```

增加一个t2.txt文件
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559516175798.png)
宿主机查看是否存在文件
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559516258116.png)

### 六、Dockerfile详解

#### 1、什么是Dockerfile？

简单一句话：Dockerfile是制作镜像的指令文件。
Dockerfile文件注意事项：

- Dockerfile文件首字母必须大写
- 制作镜像用到的文件不能再Dockerfile所在文件的父目录
- Dockerfile指令不区分大小写，不过约定俗成都用大写

#### 2、Dockerfile各指令功能详情

##### 2.1、FROM

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559686263111.png)

##### 2.2、MAINTANIER

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559686510603.png)

##### 2.3、COPY 

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559686617576.png)

##### 2.4、ADD

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559687203855.png)

##### 2.5、WORKDIR

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559687390208.png)

##### 2.6、VOLUME

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559687357740.png)

##### 2.7、EXPOSE

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559687546282.png)

##### 2.8、ENV

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559687659969.png)

##### 2.9、RUN

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559687764752.png)

##### 2.10、CMD

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559687992826.png)

##### 2.11、ENTRYPOINT

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559688261062.png)

##### 2.12、HEALTHCHCK

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559688461634.png)

##### 2.13、USER

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559688386329.png)

##### 2.14、SHLEE

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559688576800.png)

#### 3、一个Dockerfile的文件示例

```dockerfile
# This my first nginx Dockerfile
# Version 1.0

# Base images 基础镜像
FROM centos

#MAINTAINER 维护者信息
MAINTAINER tianfeiyu 

#ENV 设置环境变量
ENV PATH /usr/local/nginx/sbin:$PATH

#ADD  文件放在当前目录下，拷过去会自动解压
ADD nginx-1.8.0.tar.gz /usr/local/  
ADD epel-release-latest-7.noarch.rpm /usr/local/  

#RUN 执行以下命令 
RUN rpm -ivh /usr/local/epel-release-latest-7.noarch.rpm
RUN yum install -y wget lftp gcc gcc-c++ make openssl-devel pcre-devel pcre && yum clean all
RUN useradd -s /sbin/nologin -M www

#WORKDIR 相当于cd
WORKDIR /usr/local/nginx-1.8.0 

RUN ./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_ssl_module --with-pcre && make && make install

RUN echo "daemon off;" >> /etc/nginx.conf

#EXPOSE 映射端口
EXPOSE 80

#CMD 运行以下命令
CMD ["nginx"]
```

### 七、Docker私有Registry

#### 1、docker registry 分类

![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559900446994.png)

#### 2、安装配置registry

- 安装registry

```ebnf
yum -y install docker-registry
```

- 查看安装目录`rpm -ql docker-distribution`
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559900618581.png)
- 查看config.yml配合文件

`cd /etc/docker-distribution/registry/`

`vi config.yml`

```dts
version: 0.1
log:
  fields:
    service: registry
storage:
    cache:
        layerinfo: inmemory
    filesystem:
        rootdirectory: /var/lib/registry
http:
    addr: :5000
```

- 启动registry

```ebnf
systemctl start docker-distribution
```

- 查看下端口映射
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559901003457.png)
- 修改daemon.json配置文件，增加信任路径

```javascript
"insecure-registries":["192.168.80.128:5000"]
```

- 推送镜像到registry
  给镜像打个标签：`docker tag busybox:latest 192.168.80.128:5000/b1:v02`
  推送到仓库：`docker push 192.168.80.128:5000/b1:v02`
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559901273291.png)

#### 3、安装配置barbor

barbor是提供可是话ui的docker registry
可以去github上下载最新版的barbor：https://github.com/goharbor/harbor ，在视频教程目录也有1.8版的

- 安装
  下载下来之后放入一个自定义的目录，然后解压
  `tar xf harbor-offline-installer-v1.8.0.tgz -C /usr/local/`
  ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559901707896.png)
- 安装docker-compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

- 可以查看harbor.yml配置文件，修改host为自己的ip
- ![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559901930147.png)
  配置指南：https://github.com/goharbor/harbor/blob/master/docs/installation_guide.md
- 启动harbor

```stylus
./install.sh 
```

第一次启动较慢，启动成功，打开页面
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559904711404.png)
初始账号密码：admin，Harbor12345
![](https://gitee.com/BestErwin/img/raw/master/xiaoshujing/1559904758591.png)

------

end