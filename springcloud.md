# springcloud

![59170879834](C:\Users\Administrator\Desktop\安装\arrest\1591708798342.png)

# 1.sleuth请求链路跟踪

## 1.1下载并运行zipkin

![59170727751](C:\Users\Administrator\Desktop\安装\arrest\1591707277512.png)

## 1.2在微服务中引入依赖

![59170839231](C:\Users\Administrator\Desktop\安装\arrest\1591708392311.png)

## 1.3在配置文件中配置

![59170849259](C:\Users\Administrator\Desktop\安装\arrest\1591708492596.png)

## 1.3访问127.0.0.1/

![59170854274](C:\Users\Administrator\Desktop\安装\arrest\1591708542740.png)

# 2.nacos服务注册和配置中心

## 2.1下载安装nacos,运行naocs

<https://github.com/alibaba/nacos/releases>

启动：startup.cmd

访问：127.0.0.1：8848/nacos

![59171260375](C:\Users\Administrator\Desktop\安装\arrest\1591712603758.png)

## 2.2服务提供者注册

父pom引入依赖

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/>
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

创建cloud-alibaba-payment9001

pom依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
```

application.properties

```properties
server.port=9002
spring.application.name=payment-provider
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
management.endpoints.web.exposure.include=*
```

启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class PaymentApplication9002 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentApplication9002.class, args);
    }
}

```

controller

```java
@RestController
public class Paymentcontroller {

    @Value("${server.port}")
    private String port;

    @GetMapping("nacos/{id}")
    public String getPort(@PathVariable("id") Long id) {
        return "地址端口："+port+"\n"+"id:"+id ;
    }
}
```

同上在建一个payment9002

nacos

![59171602163](C:\Users\Administrator\Desktop\安装\arrest\1591716021631.png)

## 2.3服务消费者注册，负载均衡

创建模块cloud-alibaba-order83(省略，基本同上一样)

依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
```

## 2.4nacos与其他注册中心对比

![59175367183](C:\Users\Administrator\Desktop\安装\arrest\1591753671832.png)

![59175415025](C:\Users\Administrator\Desktop\安装\arrest\1591754150252.png)

cap

c:数据一致性

a:可用性

p:分区

## 2.5nacos之服务配置中心

创建模块config-client3377

引入依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
    </dependencies>
```

创建两个配置文件

bootstrap.yaml

```yaml
server:
  port: 3377
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
      config:
        server-addr: localhost:8848
        file-extension: yaml
#${prefix}-${spring.profile.active}.${file-extension}
# nacos-config-client-dev.yaml
```

application.yaml

```yaml
spring:
  profiles:
    active: dev #开发环境
```

启动类，controller

```
@SpringBootApplication
@EnableDiscoveryClient
public class ConfigClientApplication3377 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigClientApplication3377.class, args);
    }
}

```

要加@RefreshScope //支持nacos动态刷新功能

```
@RestController
@RefreshScope //支持nacos动态刷新功能
public class ConfigClientController {

    @Value("${config.info}")
    private String configInfo;

    public String getConfigInfo() {
        return configInfo;
    }
}

```

根据规则配置dataID

![59175745188](C:\Users\Administrator\Desktop\安装\arrest\1591757451883.png)

```
#${prefix}-${spring.profile.active}.${file-extension}

${prefix}表示spring.application.name的值，服务名
${spring.profile.active}表示开发环境
${file-extension}表示文件后缀名

例如： nacos-config-client-dev.yaml
```

![59175690464](C:\Users\Administrator\Desktop\安装\arrest\1591756904648.png)

## 2.6nacos之namespace，group,dataid之间的关系

![59175995078](C:\Users\Administrator\Desktop\安装\arrest\1591759950788.png)

![59176006235](C:\Users\Administrator\Desktop\安装\arrest\1591760062350.png)

**DataId配置**

创建dataid 

![59176707232](C:\Users\Administrator\Desktop\安装\arrest\1591767072326.png)

修改application.yaml

```
#修改后会加载test
spring:
  profiles:
    active: test #测试环境
  #active: dev #开发环境
```

**Group配置**

![59176775336](C:\Users\Administrator\Desktop\安装\arrest\1591767753365.png)

在bootstrap.yaml添加group:DEV_GROUP

![59176780618](C:\Users\Administrator\Desktop\安装\arrest\1591767806189.png)

**namespace配置**（新增一个命名空间dev）

![59176796516](C:\Users\Administrator\Desktop\安装\arrest\1591767965167.png)

在bootstrap配置namespace: id

![59176824026](C:\Users\Administrator\Desktop\安装\arrest\1591768240260.png)

## 2.7nacos集群和持久化配置（重要重要）

下载nacos到linux,解压

<https://github.com/alibaba/nacos/releases>

修改/conf/application.properties,添加以下参数，切换到mysql数据库

```
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user=root
db.password=root
```

备份修改cluster.conf.example

```
cp cluster.conf.example cluster.conf
```

进入cluster.conf,添加以下参数

```properties
#主要ip地址不能写127.0.0.1，要写本机地址
192.168.111.144:3333
192.168.111.144:4444
192.168.111.144:5555
```



nginx配置

![59178819309](C:\Users\Administrator\Desktop\安装\arrest\1591788193095.png)



![59178862318](C:\Users\Administrator\Desktop\安装\arrest\1591788623189.png)

##2.8通过docker安装配置nacos 集群和持久化

```shell
#拉取镜像
docker pull zhangbo0921/nacos-server:1.1.4

docker run -d -p 8848:8848 --name mynacos8848 zhangbo0921/nacos-server:1.1.4

#进入容器修改配置文件application.properties /opt/soft/nacos/conf 切换数据库
docker exec -it /bin/bash

#修改后从新启动容器
docker stop mynacos8848

docker restart mynacos8848


```

nginx(单点)

```
server {
        listen       80;
        server_name  www.allweing.cn;

        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        location / {
			proxy_pass http://111.229.81.116:8848;
			proxy_connect_timeout 600;
			proxy_read_timeout 600;
        }
    }
```

nginx(负载均衡)

```
upstream cluster{
    server 111.229.81.116:8848;
    server 111.229.81.116:3333;
    server 111.229.81.116:4444;
}


server {
        listen       80;
        server_name  www.allweing.cn;

        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        location / {
			proxy_pass http://cluster;
			proxy_connect_timeout 600;
			proxy_read_timeout 600;
        }
    }
```

# 3.Sentinel流量防卫兵（服务限流与熔断）

下载：<https://github.com/alibaba/Sentinel/releases>



![59179842227](C:\Users\Administrator\Desktop\安装\arrest\1591798422272.png)

运行 java -jar sentinel-dashboard.jar

访问localhost:8080

登录 sentinel

![59179889092](C:\Users\Administrator\Desktop\安装\arrest\1591798890925.png)



## 3.1创建模块cloud-alibaba-sentinel8401

依赖

```xml
<dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-datasource-nacos</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

application.yaml

```yaml
server:
  port: 8401
spring:
  application:
    name: sentinel-service
  cloud:
    nacos:
      discovery:
        server-addr: 111.229.81.116:8848
    sentinel:
      transport:
        dashboard: localhost:8080
management:
  endpoints:
    web:
      exposure:
        include: '*'
```

启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class SentinelApplication8401 {
    public static void main(String[] args) {
        SpringApplication.run(SentinelApplication8401.class, args);
    }
}

```

controller

```java
@RestController
public class FlowController {


    @GetMapping("test1")
    public String test1() {
        return "test1---------------------";
    }

    @GetMapping("test2")
    public String test2() {
        return "test2-------------------";
    }
}

```

启动模块

访问localhost:8401/test1(**sentinel懒加载，需访问一次才能注册进sentinel**)

![59180323039](C:\Users\Administrator\Desktop\安装\arrest\1591803230396.png)



![59180325103](C:\Users\Administrator\Desktop\安装\arrest\1591803251036.png)



##3.2流控规则

qps 每秒请求数

![59180416576](C:\Users\Administrator\Desktop\安装\arrest\1591804165761.png)

![59180422017](C:\Users\Administrator\Desktop\安装\arrest\1591804220176.png)



大量访问test2,导致test1挂了

![59180459116](C:\Users\Administrator\Desktop\安装\arrest\1591804591163.png)



Warm up设置

![59180618577](C:\Users\Administrator\Desktop\安装\arrest\1591806185777.png)



![59180622621](C:\Users\Administrator\Desktop\安装\arrest\1591806226211.png)



排队等待

![59180631305](C:\Users\Administrator\Desktop\安装\arrest\1591806313055.png)

![59180641855](C:\Users\Administrator\Desktop\安装\arrest\1591806418556.png)



## 3.3降级规则

![59180668118](C:\Users\Administrator\Desktop\安装\arrest\1591806681185.png)

![59180666234](C:\Users\Administrator\Desktop\安装\arrest\1591806662340.png)



 ![59183771757](C:\Users\Administrator\Desktop\安装\arrest\1591837717578.png)

## 3.4热点规则（重点）

```
@GetMapping("/hotkeys")
    @SentinelResource(value = "hotkeys",blockHandler = "deal_testHotKey")
    public String testHotKey(@RequestParam(value = "p1",required = false) String p1, @RequestParam(value = "p2",required = false) String p2) {
        return "testHotkey--------------------------";
    }

    public String deal_testHotKey(String p1, String p2, BlockException e) {
        return "testHotKey--------------------error";

    }
```

![59183967600](C:\Users\Administrator\Desktop\安装\arrest\1591839676004.png)

参数例外项

![59183981082](C:\Users\Administrator\Desktop\安装\arrest\1591839810820.png)





## 3.5系统规则

![59184010456](C:\Users\Administrator\Desktop\安装\arrest\1591840104565.png)



![59184031658](C:\Users\Administrator\Desktop\安装\arrest\1591840316584.png)



## 3.6@SentinelResource配置

全局的兜底方法

```
 @GetMapping("/testA")
    @SentinelResource(value = "testA",blockHandlerClass = MySentinelHandler.class,blockHandler = "handlerExpection1")
    public String testA() {
        return "testA++++++++++++++++++++++++++";
    }
```

```
public class MySentinelHandler {

    public static String handlerExpection1(BlockException e) {
        return "handlerExpection1++++++++++++++++++++11111111111111";
    }

    public static String handlerExpection2(BlockException e) {
        return "handlerExpection2++++++++++++++++++++222222222222222";
    }
}

```

##3.7Sentinel服务熔断

***@SentinelResoure  fallback 运行时异常 blockHandler 处理配置违规***

![59187851510](C:\Users\Administrator\Desktop\安装\arrest\1591878515107.png)

```java
 @GetMapping("/testB/{id}")
    @SentinelResource(value = "testB",fallback = "handlerRuntimeExpection")
    public String testB(@PathVariable("id") Long id) {
        if (id == 4) {
            throw new NullPointerException();
        }
        return "testB-------------------------";
    }
	//注意参数要一样
    public String handlerRuntimeExpection(@PathVariable("id") Long id,Throwable e) {
        System.out.println("---------------");
        e.printStackTrace();
        return "testB----------------------expection";
    }
```

openFeign整合服务熔断

1.激活sentinel对feign 的支持

feign.sentinel.enabled=true



2.

![59188037639](C:\Users\Administrator\Desktop\安装\arrest\1591880376398.png)

3.

![59188041597](C:\Users\Administrator\Desktop\安装\arrest\1591880415970.png)

## 3.8Sentinel规则持久化

解决问题：每次重启模块，限流规则消失

1.依赖

```xml
<dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-datasource-nacos</artifactId>
        </dependency>
```

```pr
spring:
  application:
    name: sentinel-service
  cloud:
    nacos:
      discovery:
        server-addr: 111.229.81.116:8848
    sentinel:
      transport:
        dashboard: localhost:8080
      datasource:
        ds1:
          nacos:
            server-addr: 111.229.81.116:8848
            dataId: sentinel-service
            groupId: DEFAULT_GROUP
            data-type: json
            rule-type: flow
```

![59188207911](C:\Users\Administrator\Desktop\安装\arrest\1591882079117.png)

**编写流控规则（好弱智。。。。。。。。。）**

```json
[
    {
        "resource": "/hello",
        "limitApp": "default",
        "grade": 1,
        "count": 5,
        "strategy": 0,
        "controlBehavior": 0,
        "clusterMode": false
    }
]


resource：资源名
limitApp：流控针对的调用来源，default不区分来源
grade：限流阈值类型(0-根据并发数量来限流 1-根据QPS来进行流量控制)
count：限流阈值
strategy：调用关系限流策略
controlBehavior：流量控制效果(直接拒绝、WarmUP、匀速排队)
clusterMode：是否集群模式
```

# 4.seata分布式事务

## 4.1Seata术语

TC - 事务协调者
维护全局和分支事务的状态，驱动全局事务提交或回滚。

TM - 事务管理器
定义全局事务的范围：开始全局事务、提交或回滚全局事务。

RM - 资源管理器
管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

![59192873833](C:\Users\Administrator\Desktop\安装\arrest\1591928738330.png)

##4.2seata-server安装(0.0.9)

下载地址：<https://github.com/seata/seata/releases>

备份修改file.conf

1.自定义事务组名称

2.修改事务日志储存模式为db

3.数据库连接信息

```properties
service {
  #transaction service group mapping
  vgroup_mapping.my_test_tx_group = "fsp_tx_group" #1.自定义事务组名称
  #only support when registry.type=file, please don't set multiple addresses
  default.grouplist = "127.0.0.1:8091"
  #disable seata
  disableGlobalTransaction = false
}

## transaction log store, only used in seata-server
store {
  ## store mode: file、db
  mode = "db" #2.修改事务日志储存模式为db

  ## file store property
  file {
    ## store location dir
    dir = "sessionStore"
  }

  ## database store property
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
    datasource = "dbcp"
    ## mysql/oracle/h2/oceanbase etc.
    db-type = "mysql"
    driver-class-name = "com.mysql.jdbc.Driver"
    url = "jdbc:mysql://127.0.0.1:3306/seata" #3.数据库连接信息
    user = "root"
    password = "root"
  }
}
```

4.创建数据库seata,建表

运行db_store.sql

![59193661052](C:\Users\Administrator\Desktop\安装\arrest\1591936610526.png)

5.修改registry.conf

![59193707033](C:\Users\Administrator\Desktop\安装\arrest\1591937070338.png)

6.启动nacos,seata

（。。。。未完，待续）



























# 5.Eureka

![59219970220](C:\Users\Administrator\Desktop\安装\arrest\1592199702203.png)

- Eureka：就是服务注册中心（可以是一个集群），对外暴露自己的地址
- 提供者：启动后向Eureka注册自己信息（地址，提供什么服务）
- 消费者：向Eureka订阅服务，Eureka会将对应服务的所有提供者地址列表发送给消费者，并且定期更新
- 心跳(续约)：提供者定期通过http方式向Eureka刷新自己的状态

## 5.1EurekaServer:(eureka服务端)

```xml
  <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.RC2</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>

    </dependencies>
```

```yaml
server:
  port: 10086 # 端口
spring:
  application:
    name: eureka-server # 应用名称，会在Eureka中显示
eureka:
  client:
    service-url: # EurekaServer的地址，现在是自己的地址，如果是集群，需要加上其它Server的地址。
      defaultZone: http://127.0.0.1:${server.port}/eureka
```

修改引导类，在类上添加@EnableEurekaServer注解：

```java
@SpringBootApplication
@EnableEurekaServer // 声明当前springboot应用是一个eureka服务中心
public class ItcastEurekaApplication {

    public static void main(String[] args) {
        SpringApplication.run(ItcastEurekaApplication.class, args);
    }
}
```

## 5.2EurekaClient:(eureka客户端服务提供者)：

```xml
<!-- Eureka客户端 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>



 <!-- SpringCloud的依赖,如果父工程有，可以省略-->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

```yaml
server:
  port: 8081
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/heima
    username: root
    password: root
    driverClassName: com.mysql.jdbc.Driver
  application:
    name: service-provider # 应用名称，注册到eureka后的服务名称
mybatis:
  type-aliases-package: cn.itcast.service.pojo
eureka:
  client:
    service-url: # EurekaServer地址
      defaultZone: http://127.0.0.1:10086/eureka
```

添加`@EnableDiscoveryClient`来开启Eureka客户端功能

```java
@SpringBootApplication
@EnableDiscoveryClient
public class ItcastServiceProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(ItcastServiceApplication.class, args);
    }
}
```

## 5.3EurekaClient:(eureka客户端服务消费者)：

```xml
 <!-- Eureka客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
    </dependencies>
    
    
    
    
     <!-- SpringCloud的依赖,如果父工程有，可以省略-->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

```yaml
server:
  port: 80
spring:
  application:
    name: service-consumer
eureka:
  client:
    service-url:
      defaultZone: http://localhost:10086/eureka
```

在启动类添加@EnableDiscoveryClient开启Eureka客户端

```java
@SpringBootApplication
@EnableDiscoveryClient // 开启Eureka客户端
public class ItcastServiceConsumerApplication {

    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

    public static void main(String[] args) {
        SpringApplication.run(ItcastServiceConsumerApplication.class, args);
    }
}
```

## 5.4Eureka架构中的三个核心角色：

- 服务注册中心

  Eureka的服务端应用，提供服务注册和发现功能，就是刚刚我们建立的itcast-eureka。

  ```yaml
  #开发环境配置，生产环境不需要配置
  eureka:
    server:
      enable-self-preservation: false # 关闭自我保护模式（缺省为打开）
      eviction-interval-timer-in-ms: 1000 # 扫描失效服务的间隔时间（缺省为60*1000ms）
  ```

  ​

- 服务提供者

  提供服务的应用，可以是SpringBoot应用，也可以是其它任意技术实现，只要对外提供的是Rest风格服务即可。本例中就是我们实现的itcast-service-provider。服务提供者要向EurekaServer注册服务，并且完成服务续约等工作。

  ```yaml
  #开发环境配置，生产环境不需要配置
  eureka:
    instance:
      lease-expiration-duration-in-seconds: 10
      lease-renewal-interval-in-seconds: 5
  ```

  - lease-renewal-interval-in-seconds：服务续约(renew)的间隔，默认为30秒
  - lease-expiration-duration-in-seconds：服务失效时间，默认值90秒

- 服务消费者

  消费应用从注册中心获取服务列表，从而得知每个服务方的信息，知道去哪里调用服务方。本例中就是我们实现的itcast-service-consumer。获取服务列表:

  ```yaml
  #开发环境配置，生产环境不需要配置
  eureka:
    client:
      registry-fetch-interval-seconds: 5 #表示每隔5s获取一次服务列表
  ```

  ​





# 6.负载均衡Ribbon

Eureka中已经帮我们集成了负载均衡组件：Ribbon，简单修改代码即可使用

在RestTemplate的配置方法上添加`@LoadBalanced`注解（服务消费方）

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

Ribbon默认的负载均衡策略是简单的轮询

修改负载均衡的策略

SpringBoot也帮我们提供了修改负载均衡规则的配置入口，在application.yml中添加如下配置：

格式是：`{服务名称}.ribbon.NFLoadBalancerRuleClassName`，值就是IRule的实现类。

```yaml
service-provider: #服务名称
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
    
```



# 7.Hystrix容错组件

## 7.1Hystrix是什么

Hystix是Netflix开源的一个延迟和容错库，用于隔离访问远程服务、第三方库，防止出现级联失败

微服务发生异常，请求阻塞，用户不会得到响应，则tomcat的这个线程不会释放，于是越来越多的用户请求到来，越来越多的线程会阻塞.服务器支持的线程和并发数有限，请求一直阻塞，会导致服务器资源耗尽，从而导致所有其它服务都不可用，形成雪崩效应。

Hystix解决雪崩问题的手段有两个：

- 线程隔离，服务降级
- 服务熔断

## 7.2线程隔离，服务降级

![59220244313](C:\Users\Administrator\Desktop\安装\arrest\1592202443132.png)

1.引入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

2.开启熔断，在启动类添加@EnableCircuitBreak

![59220266181](C:\Users\Administrator\Desktop\安装\arrest\1592202661814.png)

3.编写降级逻辑

```java
 @GetMapping
    @ResponseBody
    @HystrixCommand(fallbackMethod = "queryUserByIdFallBack")
    public String queryUserById(@RequestParam("id") Long id) {
        String user = this.restTemplate.getForObject("http://service-provider/user/" + id, String.class);
        return user;
    }

    public String queryUserByIdFallBack(Long id){
        return "请求繁忙，请稍后再试！";
    }
```

要注意，因为熔断的降级逻辑方法必须跟正常逻辑方法保证：**相同的参数列表和返回值声明**。

@HystrixCommand(fallbackMethod = "queryByIdFallBack")：用来声明一个降级逻辑的方法



4.把Fallback配置加在类上，实现默认fallback:

- @DefaultProperties(defaultFallback = "defaultFallBack")：在类上指明统一的失败降级方法
- @HystrixCommand：在方法上直接使用该注解，使用默认的剪辑方法。
- defaultFallback：默认降级方法，不用任何参数，以匹配更多方法，但是返回值一定一致

```java
@Controller
@RequestMapping("consumer/user")
@DefaultProperties(defaultFallback = "fallBackMethod") // 指定一个类的全局熔断方法
public class UserController {

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping
    @ResponseBody
    @HystrixCommand // 标记该方法需要熔断
    public String queryUserById(@RequestParam("id") Long id) {
        String user = this.restTemplate.getForObject("http://service-provider/user/" + id, String.class);
        return user;
    }

    /**
     * 熔断方法
     * 返回值要和被熔断的方法的返回值一致
     * 熔断方法不需要参数
     * @return
     */
    public String fallBackMethod(){
        return "请求繁忙，请稍后再试！";
    }
}
```

5.在之前的案例中，请求在超过1秒后都会返回错误信息，这是因为Hystix的默认超时时长为1，我们可以通过配置修改这个值：

我们可以通过`hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds`来设置Hystrix超时时间。该配置没有提示。

```java
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 6000 # 设置hystrix的超时时间为6000ms
```

## 7.3服务熔断

熔断状态机3个状态：

- Closed：关闭状态，所有请求都正常访问。
- Open：打开状态，所有请求都会被降级。Hystix会对请求情况计数，当一定时间内失败请求百分比达到阈值，则触发熔断，断路器会完全打开。默认失败比例的阈值是50%，请求次数最少不低于20次。
- Half Open：半开状态，open状态不是永久的，打开后会进入休眠时间（默认是5S）。随后断路器会自动进入半开状态。此时会释放部分请求通过，若这些请求都是健康的，则会完全关闭断路器，否则继续保持打开，再次进行休眠计时

为了能够精确控制请求的成功或失败，我们在consumer的调用业务中加入一段逻辑：

```java
@GetMapping("{id}")
@HystrixCommand
public String queryUserById(@PathVariable("id") Long id){
    if(id == 1){
        throw new RuntimeException("太忙了");
    }
    String user = this.restTemplate.getForObject("http://service-provider/user/" + id, String.class);
    return user;
}
```

当我们疯狂访问id为1的请求时（超过20次），就会触发熔断。断路器会断开，一切请求都会被降级处理。

此时你访问id为2的请求，会发现返回的也是失败，而且失败时间很短，只有几毫秒左右：

不过，默认的熔断触发要求较高，休眠时间窗较短，为了测试方便，我们可以通过配置修改熔断策略:

```
circuitBreaker.requestVolumeThreshold=10
circuitBreaker.sleepWindowInMilliseconds=10000
circuitBreaker.errorThresholdPercentage=50
```

解读：

- requestVolumeThreshold：触发熔断的最小请求次数，默认20
- errorThresholdPercentage：触发熔断的失败请求最小占比，默认50%
- sleepWindowInMilliseconds：休眠时长，默认是5000毫秒

# 8.Feign

## 8.1快速入门

导入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

在启动类上，**添加注解@EnableFeignClients**，开启Feign功能

```java
@SpringCloudApplication
@EnableFeignClients // 开启feign客户端
public class ItcastServiceConsumerApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(ItcastServiceConsumerApplication.class, args);
    }
}
```

feign已经自动集成了Ribbon负载均衡的RestTemplate

Feign的客户端:

```java
@FeignClient(value = "service-provider") // 标注该类是一个feign接口
public interface UserClient {

    @GetMapping("user/{id}")
    User queryById(@PathVariable("id") Long id);
}
```

调用UserClient接口

```java
@Controller
@RequestMapping("consumer/user")
public class UserController {

    @Autowired
    private UserClient userClient;

    @GetMapping
    @ResponseBody
    public User queryUserById(@RequestParam("id") Long id){
        User user = this.userClient.queryUserById(id);
        return user;
    }

}
```

负载均衡,Feign中本身已经集成了Ribbon依赖和自动配置

## 8.2Feign整合Hystrix

Feign默认也有对Hystrix的集成，默认情况下是关闭的。我们需要通过下面的参数来开启：

```yaml
feign:
  hystrix:
    enabled: true # 开启Feign的熔断功能
```

Feign中的Fallback配置不像hystrix中那样简单

1）首先，我们要定义一个类UserClientFallback，实现刚才编写的UserClient，作为fallback的处理类

```java
@Component
public class UserClientFallback implements UserClient {

    @Override
    public User queryById(Long id) {
        User user = new User();
        user.setUserName("服务器繁忙，请稍后再试！");
        return user;
    }
}
```

然后在UserClient中，指定刚才编写的实现类

```java
@FeignClient(value = "service-provider", fallback = UserClientFallback.class) // 标注该类是一个feign接口
public interface UserClient {

    @GetMapping("user/{id}")
    User queryUserById(@PathVariable("id") Long id);
}
```

## 8.3OpenFeign日志增强（了解）

主要作用：查看feign调用接口的详细信息

![59220982796](C:\Users\Administrator\Desktop\安装\arrest\1592209827965.png)

```java
@Configuration
public class FeignLogConfiguration {

    @Bean
    Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }
}
```

![59221015934](C:\Users\Administrator\Desktop\安装\arrest\1592210159346.png)



```java
logging:
  level:
    cn.itcast: debug
```

在FeignClient中指定配置类(也可不配置)

```java
@FeignClient(value = "service-privider", fallback = UserFeignClientFallback.class, configuration = FeignConfig.class)
public interface UserFeignClient {
    @GetMapping("/user/{id}")
    User queryUserById(@PathVariable("id") Long id);
}
```

重启项目，即可看到每次访问的日志

![59221032795](C:\Users\Administrator\Desktop\安装\arrest\1592210327955.png)

## 8.4设置feign客户端的超时时间

作用：消费者调用服务提供者feigin默认时间是1s,如果服务提供者业务耗时较长，会导致报错

![59221099901](C:\Users\Administrator\Desktop\安装\arrest\1592210999018.png)

```yaml
ribbon:
	ReadTimeout: 5000
	ConnecTimeout: 5000
```



# 9.Zuul网关

## 9.1快速入门

1.引入依赖

2.编写配置

3.通过在引导类添加@EnableZuulProxy `注解开启Zuul的功能

```java
@SpringBootApplication
@EnableZuulProxy // 开启网关功能
public class ItcastZuulApplication {

    public static void main(String[] args) {
        SpringApplication.run(ItcastZuulApplication.class, args);
    }
}
```

4.编写路由规则(**将网关也注册到eureka中**)

```
zuul:
  routes:
    service-provider: # 这里是路由id，随意写
      path: /service-provider/** # 这里是映射路径
      serviceId: service-provider # 指定服务名称
```

简化路由规则

```yaml
zuul:
  routes:
    service-provider: /service-provider/** # 这里是映射路径
```

默认的路由规则

- 默认情况下，一切服务的映射路径就是服务名本身。例如服务名为：`service-provider`，则默认的映射路径就	是：`/service-provider/**`

也就是说，刚才的映射规则我们完全不配置也是OK的，不信就试试看

路由前缀

```yaml
zuul:
  routes:
    service-provider: /service-provider/**
    service-consumer: /service-consumer/**
  prefix: /api # 添加路由前缀
```

## 9.2过滤器

zuul作为网关的其中一个重要功能，就是实现请求的鉴权。而这个动作我们往往是通过Zuul提供的过滤器来实现的。

```java
public abstract ZuulFilter implements IZuulFilter{

    abstract public String filterType();

    abstract public int filterOrder();
    
    boolean shouldFilter();// 来自IZuulFilter

    Object run() throws ZuulException;// IZuulFilter
}
```

- `shouldFilter`：返回一个`Boolean`值，判断该过滤器是否需要执行。返回true执行，返回false不执行。

- `run`：过滤器的具体业务逻辑。

- `filterType`：返回字符串，代表过滤器的类型。包含以下4种：

  - `pre`：请求在被路由之前执行
  - `route`：在路由请求时调用
  - `post`：在route和errror过滤器之后调用
  - `error`：处理请求时发生错误调用

- `filterOrder`：通过返回的int值来定义过滤器的执行顺序，数字越小优先级越高。

  ​

生命周期图:

![59220880351](C:\Users\Administrator\Desktop\安装\arrest\1592208803512.png)

**正常流程**：

- 请求到达首先会经过pre类型过滤器，而后到达route类型，进行路由，请求就到达真正的服务提供者，执行请求，返回结果后，会到达post过滤器。而后返回响应。

**异常流程：**

- 整个过程中，pre或者route过滤器出现异常，都会直接进入error过滤器，在error处理完毕后，会将请求交给POST过滤器，最后返回给用户。
- 如果是error过滤器自己出现异常，最终也会进入POST过滤器，将最终结果返回给请求客户端。
- 如果是POST过滤器出现异常，会跳转到error过滤器，但是与pre和route不同的是，请求不会再到达POST过滤器了。

**使用场景**

- 请求鉴权：一般放在pre类型，如果发现没有访问权限，直接就拦截了

- 异常处理：一般会在error类型和post类型过滤器中结合来处理。

- 服务调用时长统计：pre和post结合使用。

  ​

## 9.3自定义过滤器

自定义一个过滤器，模拟一个登录的校验。基本逻辑：如果请求中有access-token参数，则认为请求有效，放行。

```java
@Component
public class LoginFilter extends ZuulFilter {
    /**
     * 过滤器类型，前置过滤器
     * @return
     */
    @Override
    public String filterType() {
        return "pre";
    }

    /**
     * 过滤器的执行顺序
     * @return
     */
    @Override
    public int filterOrder() {
        return 1;
    }

    /**
     * 该过滤器是否生效
     * @return
     */
    @Override
    public boolean shouldFilter() {
        return true;
    }

    /**
     * 登陆校验逻辑
     * @return
     * @throws ZuulException
     */
    @Override
    public Object run() throws ZuulException {
        // 获取zuul提供的上下文对象
        RequestContext context = RequestContext.getCurrentContext();
        // 从上下文对象中获取请求对象
        HttpServletRequest request = context.getRequest();
        // 获取token信息
        String token = request.getParameter("access-token");
        // 判断
        if (StringUtils.isBlank(token)) {
            // 过滤该请求，不对其进行路由
            context.setSendZuulResponse(false);
            // 设置响应状态码，401
            context.setResponseStatusCode(HttpStatus.SC_UNAUTHORIZED);
            // 设置响应信息
            context.setResponseBody("{\"status\":\"401\", \"text\":\"request error!\"}");
        }
        // 校验通过，把登陆信息放入上下文信息，继续向后执行
        context.set("token", token);
        return null;
    }
}
```

## 9.4负载均衡和熔断

Zuul中默认就已经集成了Ribbon负载均衡和Hystix熔断机制。但是所有的超时策略都是走的默认值，比如熔断超时时间只有1S，很容易就触发了。因此建议我们手动进行配置

```
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 2000 # 设置hystrix的超时时间为6000ms
```









# 10.Gateway（route,predicate,filter）

![59222564459](C:\Users\Administrator\Desktop\安装\arrest\1592225644594.png)





![59222605018](C:\Users\Administrator\Desktop\安装\arrest\1592226050189.png)

![59222609124](C:\Users\Administrator\Desktop\安装\arrest\1592226091245.png)



##10.1route(动态路由)

![59222646376](C:\Users\Administrator\Desktop\安装\arrest\1592226463761.png)



实现动态路由

![59222713331](C:\Users\Administrator\Desktop\安装\arrest\1592227133319.png)

##10.2predicate(断言)

获取时区时间

![59222744776](C:\Users\Administrator\Desktop\安装\arrest\1592227447765.png)



![59222799180](C:\Users\Administrator\Desktop\安装\arrest\1592227991809.png)



![59222801922](C:\Users\Administrator\Desktop\安装\arrest\1592228019224.png)

## 10.3filter（过滤器）



![59222827052](C:\Users\Administrator\Desktop\安装\arrest\1592228270525.png)



![59222840495](C:\Users\Administrator\Desktop\安装\arrest\1592228404950.png)



![59222863216](C:\Users\Administrator\Desktop\安装\arrest\1592228632162.png)



getOrder: 返回的值越小，优先级越高











































