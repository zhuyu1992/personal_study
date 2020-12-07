# springboot

## 1.日志

### 1.1日志框架简介

![](C:\Users\Administrator\Desktop\安装\arrest\1588149685729.png)

### 1.2测试

![](C:\Users\Administrator\Desktop\安装\arrest\1588150730731.png)

### 1.3调整日志级别

![](C:\Users\Administrator\Desktop\安装\arrest\1588150922419.png)

### 1.4日志的输出位置

![](C:\Users\Administrator\Desktop\安装\arrest\1588151064047.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588151197558.png)

### 1.5日志的输出格式

![](C:\Users\Administrator\Desktop\安装\arrest\1588151362197.png)

### 1.6指定日志文件

![](C:\Users\Administrator\Desktop\安装\arrest\1588151994935.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588152051880.png)

logback-spring.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration  scan="true" scanPeriod="10 seconds">
    <!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
    <!-- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true -->
    <!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
    <!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。 -->

    <contextName>logback</contextName>
    <!-- name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。 -->
    <property name="log.path" value="D:/guli_1010/edu" />

    <!-- 彩色日志 -->
    <!-- 配置格式变量：CONSOLE_LOG_PATTERN 彩色日志格式 -->
    <!-- magenta:洋红 -->
    <!-- boldMagenta:粗红-->
    <!-- cyan:青色 -->
    <!-- white:白色 -->
    <!-- magenta:洋红 -->
    <property name="CONSOLE_LOG_PATTERN"
              value="%yellow(%date{yyyy-MM-dd HH:mm:ss}) |%highlight(%-5level) |%blue(%thread) |%blue(%file:%line) |%green(%logger) |%cyan(%msg%n)"/>


    <!--输出到控制台-->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <!--此日志appender是为开发使用，只配置最底级别，控制台输出的日志级别是大于或等于此级别的日志信息-->
        <!-- 例如：如果此处配置了INFO级别，则后面其他位置即使配置了DEBUG级别的日志，也不会被输出 -->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
        <encoder>
            <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
            <!-- 设置字符集 -->
            <charset>UTF-8</charset>
        </encoder>
    </appender>


    <!--输出到文件-->

    <!-- 时间滚动输出 level为 INFO 日志 -->
    <appender name="INFO_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/log_info.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 每天日志归档路径以及格式 -->
            <fileNamePattern>${log.path}/info/log-info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录info级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 时间滚动输出 level为 WARN 日志 -->
    <appender name="WARN_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/log_warn.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <charset>UTF-8</charset> <!-- 此处设置字符集 -->
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/warn/log-warn-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录warn级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>warn</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <!-- 时间滚动输出 level为 ERROR 日志 -->
    <appender name="ERROR_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/log_error.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <charset>UTF-8</charset> <!-- 此处设置字符集 -->
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/error/log-error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录ERROR级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!--
        <logger>用来设置某一个包或者具体的某一个类的日志打印级别、以及指定<appender>。
        <logger>仅有一个name属性，
        一个可选的level和一个可选的addtivity属性。
        name:用来指定受此logger约束的某一个包或者具体的某一个类。
        level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，
              如果未设置此属性，那么当前logger将会继承上级的级别。
    -->
    <!--
        使用mybatis的时候，sql语句是debug下才会打印，而这里我们只配置了info，所以想要查看sql语句的话，有以下两种操作：
        第一种把<root level="INFO">改成<root level="DEBUG">这样就会打印sql，不过这样日志那边会出现很多其他消息
        第二种就是单独给mapper下目录配置DEBUG模式，代码如下，这样配置sql语句会打印，其他还是正常DEBUG级别：
     -->
    <!--开发环境:打印控制台-->
    <springProfile name="dev">
        <!--可以输出项目中的debug日志，包括mybatis的sql日志-->
        <logger name="com.guli" level="INFO" />

        <!--
            root节点是必选节点，用来指定最基础的日志输出级别，只有一个level属性
            level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，默认是DEBUG
            可以包含零个或多个appender元素。
        -->
        <root level="INFO">
            <appender-ref ref="CONSOLE" />
            <appender-ref ref="INFO_FILE" />
            <appender-ref ref="WARN_FILE" />
            <appender-ref ref="ERROR_FILE" />
        </root>
    </springProfile>


    <!--生产环境:输出到文件-->
    <springProfile name="pro">

        <root level="INFO">
            <appender-ref ref="CONSOLE" />
            <appender-ref ref="DEBUG_FILE" />
            <appender-ref ref="INFO_FILE" />
            <appender-ref ref="ERROR_FILE" />
            <appender-ref ref="WARN_FILE" />
        </root>
    </springProfile>

</configuration>
```

## 2.异常处理

### 2.1创建自定义异常类

```
@Data
@AllArgsConstructor
@NoArgsConstructor
public class GuliException extends RuntimeException {

    @ApiModelProperty(value = "状态码")
    private Integer code;

    private String msg;
    
}
```

### 2.2业务中需要的位置抛出GuliException

```
try {
    int a = 10/0;
}catch(Exception e) {
    throw new GuliException(20001,"出现自定义异常");
}
```

###2.3添加异常处理方法 GlobalExceptionHandler.java中添加

```
@ExceptionHandler(GuliException.class)
@ResponseBody
public R error(GuliException e){
    e.printStackTrace();
    return R.error().message(e.getMsg()).code(e.getCode());
}
```

```
/**
 * 统一异常处理类
 */
@ControllerAdvice
public class GlobalExceptionHandler {

	@ExceptionHandler(Exception.class)
	@ResponseBody
	public R error(Exception e){
		e.printStackTrace();
		return R.error();
	}
}
```

### 2.4自定义错误页面(在templates下创建error.html)

![58966373524](C:\Users\Administrator\Desktop\安装\arrest\1589663735242.png)



## 3.快速简单入门

### 3.1创建一个maven工程

###3.2导入spring boot相关的依赖

```
  <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### 3.3编写一个主程序；启动Spring Boot应用

```
/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {

        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }
}
```

### 3.4编写相关的Controller、Service

```
@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World!";
    }
}
```

### 3.5运行主程序测试

### 3.6简化部署

```
<!-- 这个插件，可以将应用打包成一个可执行的jar包；-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

## 4.配置文件

### 4.1配置文件值注入

配置文件

```
person:
    lastName: hello
    age: 18
    boss: false
    birth: 2017/12/12
    maps: {k1: v1,k2: 12}
    lists:
      - lisi
      - zhaoliu
    dog:
      name: 小狗
      age: 12
```

javaBean

```
/**
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
 *      prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
 *
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
```

### 4.2@Value获取值和@ConfigurationProperties获取值比较

![](C:\Users\Administrator\Desktop\安装\arrest\1589507943051.png)

### 4.3配置文件注入值数据校验

```
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person {

    /**
     * <bean class="Person">
     *      <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#{SpEL}"></property>
     * <bean/>
     */

   //lastName必须是邮箱格式
    @Email
    //@Value("${person.last-name}")
    private String lastName;
    //@Value("#{11*2}")
    private Integer age;
    //@Value("true")
    private Boolean boss;

    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
```

### 4.4@PropertySource&@ImportResource&@Bean

#### 4.4.1@PropertySource：加载指定的配置文件；

```
/**
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
 *      prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
 *  @ConfigurationProperties(prefix = "person")默认从全局配置文件中获取值；
 *
 */
@PropertySource(value = {"classpath:person.properties"})
@Component
@ConfigurationProperties(prefix = "person")
//@Validated
public class Person {

    /**
     * <bean class="Person">
     *      <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#{SpEL}"></property>
     * <bean/>
     */

   //lastName必须是邮箱格式
   // @Email
    //@Value("${person.last-name}")
    private String lastName;
    //@Value("#{11*2}")
    private Integer age;
    //@Value("true")
    private Boolean boss;

```

#### 4.4.2导入Spring的配置文件，让配置文件里面的内容生效；

@ImportResource（了解）

SpringBoot推荐给容器中添加组件的方式，使用全注解的方式（推荐）

1、配置类@Configuration------>Spring配置文件

2、使用@Bean给容器中添加组件

```
/**
 * @Configuration：指明当前类是一个配置类；就是来替代之前的Spring配置文件
 *
 * 在配置文件中用<bean><bean/>标签添加组件
 *
 */
@Configuration
public class MyAppConfig {

    //将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
    @Bean
    public HelloService helloService02(){
        System.out.println("配置类@Bean给容器中添加组件了...");
        return new HelloService();
    }
}
```

### 4.5配置文件占位符

1、随机数

```
${random.value}、${random.int}、${random.long}
${random.int(10)}、${random.int[1024,65536]}

```

2、占位符获取之前配置的值，如果没有可以是用:指定默认值

```
person.last-name=张三${random.uuid}
person.age=${random.int}
person.birth=2017/12/15
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=${person.hello:hello}_dog
person.dog.age=15
```

### 4.6Profile

#### 1、多Profile文件

我们在主配置文件编写的时候，文件名可以是   application-{profile}.properties/yml

默认使用application.properties的配置；

#### 2、yml支持多文档块方式（了解）

```

server:
  port: 8081
spring:
  profiles:
    active: prod

---
server:
  port: 8083
spring:
  profiles: dev


---

server:
  port: 8084
spring:
  profiles: prod  #指定属于哪个环境
```

#### 3、激活指定profile

1、在配置文件中指定  spring.profiles.active=dev

	2、命令行：
	
		java -jar spring-boot-02-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev；
	
		可以直接在测试的时候，配置传入命令行参数
	
	3、虚拟机参数；
	
		-Dspring.profiles.active=dev
### 4.7配置文件加载位置(了解)

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

–file:./config/

–file:./

–classpath:/config/

–classpath:/

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

SpringBoot会从这四个位置全部加载主配置文件；互补配置；



==我们还可以通过spring.config.location来改变默认的配置文件位置==

项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默认加载的这些配置文件共同起作用形成互补配置；

java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --spring.config.location=G:/application.properties

### 4.8自动配置原理（重点）

1.SpringBoot启动的时候加载主配置类，开启了自动配置功能 ==@EnableAutoConfiguration==

2.@EnableAutoConfiguration 作用：

- 利用EnableAutoConfigurationImportSelector给容器中导入一些组件
- 可以查看selectImports()方法的内容；
- List<String> configurations = getCandidateConfigurations(annotationMetadata,      attributes);获取候选的配置

```
SpringFactoriesLoader.loadFactoryNames()
扫描所有jar包类路径下  META-INF/spring.factories
把扫描到的这些文件的内容包装成properties对象
从properties中获取到EnableAutoConfiguration.class类（类名）对应的值，然后把他们添加在容器中
```

将 类路径下  META-INF/spring.factories 里面配置的所有EnableAutoConfiguration的值加入到了容器中；

```
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
org.springframework.boot.autoconfigure.elasticsearch.jest.JestAutoConfiguration,\
org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
org.springframework.boot.autoconfigure.mobile.DeviceResolverAutoConfiguration,\
org.springframework.boot.autoconfigure.mobile.DeviceDelegatingViewResolverAutoConfiguration,\
org.springframework.boot.autoconfigure.mobile.SitePreferenceAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
org.springframework.boot.autoconfigure.reactor.ReactorAutoConfiguration,\
org.springframework.boot.autoconfigure.security.SecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.SecurityFilterAutoConfiguration,\
org.springframework.boot.autoconfigure.security.FallbackWebSecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.OAuth2AutoConfiguration,\
org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
org.springframework.boot.autoconfigure.social.SocialWebAutoConfiguration,\
org.springframework.boot.autoconfigure.social.FacebookAutoConfiguration,\
org.springframework.boot.autoconfigure.social.LinkedInAutoConfiguration,\
org.springframework.boot.autoconfigure.social.TwitterAutoConfiguration,\
org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
org.springframework.boot.autoconfigure.web.DispatcherServletAutoConfiguration,\
org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration,\
org.springframework.boot.autoconfigure.web.ErrorMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.web.HttpEncodingAutoConfiguration,\
org.springframework.boot.autoconfigure.web.HttpMessageConvertersAutoConfiguration,\
org.springframework.boot.autoconfigure.web.MultipartAutoConfiguration,\
org.springframework.boot.autoconfigure.web.ServerPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.web.WebClientAutoConfiguration,\
org.springframework.boot.autoconfigure.web.WebMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.WebSocketAutoConfiguration,\
org.springframework.boot.autoconfigure.websocket.WebSocketMessagingAutoConfiguration,\
org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration
```

每一个这样的  xxxAutoConfiguration类都是容器中的一个组件，都加入到容器中；用他们来做自动配置；

每一个自动配置类进行自动配置功能

**以HttpEncodingAutoConfiguration（Http编码自动配置）为例解释自动配置原理**

```
@Configuration   //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件
@EnableConfigurationProperties(HttpEncodingProperties.class)  //启动指定类的ConfigurationProperties功能；将配置文件中对应的值和HttpEncodingProperties绑定起来；并把HttpEncodingProperties加入到ioc容器中

@ConditionalOnWebApplication //Spring底层@Conditional注解（Spring注解版），根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效；    判断当前应用是否是web应用，如果是，当前配置类生效

@ConditionalOnClass(CharacterEncodingFilter.class)  //判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；

@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)  //判断配置文件中是否存在某个配置  spring.http.encoding.enabled；如果不存在，判断也是成立的
//即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；
public class HttpEncodingAutoConfiguration {
  
  	//他已经和SpringBoot的配置文件映射了
  	private final HttpEncodingProperties properties;
  
   //只有一个有参构造器的情况下，参数的值就会从容器中拿
  	public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
		this.properties = properties;
	}
  
    @Bean   //给容器中添加一个组件，这个组件的某些值需要从properties中获取
	@ConditionalOnMissingBean(CharacterEncodingFilter.class) //判断容器没有这个组件？
	public CharacterEncodingFilter characterEncodingFilter() {
		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
		filter.setEncoding(this.properties.getCharset().name());
		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
		return filter;
	}
```

根据当前不同的条件判断，决定这个配置类是否生效？

一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；

所有在配置文件中能配置的属性都是在xxxxProperties类中封装者‘；配置文件能配置什么就可以参照某个功能对应的这个属性类

```
@ConfigurationProperties(prefix = "spring.http.encoding")  //从配置文件中获取指定的值和bean的属性进行绑定
public class HttpEncodingProperties {

   public static final Charset DEFAULT_CHARSET = Charset.forName("UTF-8");
```

**精髓：**

​	**1）、SpringBoot启动会加载大量的自动配置类**

​	**2）、我们看我们需要的功能有没有SpringBoot默认写好的自动配置类；**

​	**3）、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）**

​	**4）、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值；**

xxxxAutoConfigurartion：自动配置类；

给容器中添加组件

xxxxProperties:封装配置文件中相关属性

***细节***

@Conditional派生注解（Spring注解版原生的@Conditional作用）

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

| @Conditional扩展注解            | 作用（判断是否满足当前指定条件）                 |
| ------------------------------- | ------------------------------------------------ |
| @ConditionalOnJava              | 系统的java版本是否符合要求                       |
| @ConditionalOnBean              | 容器中存在指定Bean；                             |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean；                           |
| @ConditionalOnExpression        | 满足SpEL表达式指定                               |
| @ConditionalOnClass             | 系统中有指定的类                                 |
| @ConditionalOnMissingClass      | 系统中没有指定的类                               |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                   |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                     |
| @ConditionalOnWebApplication    | 当前是web环境                                    |
| @ConditionalOnNotWebApplication | 当前不是web环境                                  |
| @ConditionalOnJndi              | JNDI存在指定项                                   |

**自动配置类必须在一定的条件下才能生效；**

我们怎么知道哪些自动配置类生效；

**==我们可以通过启用  debug=true属性；来让控制台打印自动配置报告==**，这样我们就可以很方便的知道哪些自动配置类生效；

```
=========================
AUTO-CONFIGURATION REPORT
=========================


Positive matches:（自动配置类启用的）
-----------------

   DispatcherServletAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet'; @ConditionalOnMissingClass did not find unwanted class (OnClassCondition)
      - @ConditionalOnWebApplication (required) found StandardServletEnvironment (OnWebApplicationCondition)
        
    
Negative matches:（没有启动，没有匹配成功的自动配置类）
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'javax.jms.ConnectionFactory', 'org.apache.activemq.ActiveMQConnectionFactory' (OnClassCondition)

   AopAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'org.aspectj.lang.annotation.Aspect', 'org.aspectj.lang.reflect.Advice' (OnClassCondition)
        
```

启动器

- 说白了就是spring boot的启动场景
- 比如spring-boot-starter-web,就会帮我们自动导入web环境所有的依赖
- springboot会将所有的功能场景变成一个个启动器
- 我们要什么功能，引入相应的启动器就可以了

自动配置过程

1. @SpringBootApplication
2. @EnableAutoConfiguration   
3. @Import(AutoConfigurationImportSelector.class)
4. selectImports（）
5. getAutoConfigurationEntry（）
6. getCandidateConfigurations（）
7. loadFactoryNames（）
8. loadSpringFactories（）
9. getResources("META-INF/spring.factories")
10. **简单点说，就是加载spring.factories文件中的自动配置类，这些自动配置类根据引入的jar包等条件判断是否起作用，引入相应的组件到容器中**

## 5.springboot和缓存

### 5.1JSR107

![](C:\Users\Administrator\Desktop\安装\arrest\1589550501485.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589550654802.png)

### 5.2Spring缓存抽象

#### 5.2.1几个重要概念&缓存注解

![](C:\Users\Administrator\Desktop\安装\arrest\1589553861159.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589553913665.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589554116439.png)



### 5.3缓存使用

• 1、引入spring-boot-starter-cache模块 

• 2、@EnableCaching开启缓存 

• 3、使用缓存注解 

• 4、切换为其他缓存

![](C:\Users\Administrator\Desktop\安装\arrest\1589603211537.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589603427032.png)

@Cacheing 组合注解

![](C:\Users\Administrator\Desktop\安装\arrest\1589603483997.png)

@CacheConfig(处理缓存的公共配置)

![](C:\Users\Administrator\Desktop\安装\arrest\1589603330409.png)



### 5.4缓存整合redis

1.安装redis

2.引入redis启动器

3.配置文件中添加

![](C:\Users\Administrator\Desktop\安装\arrest\1589604200137.png)

4.redis操作

![](C:\Users\Administrator\Desktop\安装\arrest\1589604521845.png)



![](C:\Users\Administrator\Desktop\安装\arrest\1589604659051.png)



```
@Configuration
public class MyRedis {


/*
         redisTemplate 默认使用JDK的序列化机制, 存储二进制字节码, 所以自定义序列化类
      @param redisConnectionFactory redis连接工厂类
     @return RedisTemplate*/


    @Bean(name = "redisTemplate")
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){

        RedisTemplate<String,Object> redisTemplate = new RedisTemplate<>();

        redisTemplate.setConnectionFactory(redisConnectionFactory);
        redisTemplate.setKeySerializer(keySerializer());
        redisTemplate.setHashKeySerializer(keySerializer());
        redisTemplate.setValueSerializer(valueSerializer());
        redisTemplate.setHashValueSerializer(valueSerializer());
        return redisTemplate;
    }

    @Primary
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory redisConnectionFactory){
        //缓存配置对象
        RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig();

        redisCacheConfiguration = redisCacheConfiguration.entryTtl(Duration.ofMinutes(30L)) //设置缓存的默认超时时间：30分钟
                .disableCachingNullValues()             //如果是空值，不缓存
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(keySerializer()))         //设置key序列化器
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer((valueSerializer())));  //设置value序列化器

        return RedisCacheManager
                .builder(RedisCacheWriter.nonLockingRedisCacheWriter(redisConnectionFactory))
                .cacheDefaults(redisCacheConfiguration).build();
    }


    private RedisSerializer<String> keySerializer() {
        return new StringRedisSerializer();
    }

    private RedisSerializer<Object> valueSerializer() {
        return new GenericJackson2JsonRedisSerializer();
    }
}
```

## 6.springboot,异步任务，定时任务，邮件任务



### 6.1异步任务

![](C:\Users\Administrator\Desktop\安装\arrest\1589620558688.png)

**快速相应，加@Async的方法，spring会开启另一个线程执行**

![](C:\Users\Administrator\Desktop\安装\arrest\1589619997361.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589620032630.png)



### 6.2定时任务（重点）

![](C:\Users\Administrator\Desktop\安装\arrest\1589620504667.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589620947698.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589621417793.png)



![](C:\Users\Administrator\Desktop\安装\arrest\1589621850633.png)

### 6.3邮件任务

![](C:\Users\Administrator\Desktop\安装\arrest\1589622038841.png)

```
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
        </dependency>
```

![](C:\Users\Administrator\Desktop\安装\arrest\1589622565413.png)



![](C:\Users\Administrator\Desktop\安装\arrest\1589622620347.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589622861713.png)

## 7.Spring Boot与开发热部署（了解）

![58965319810](C:\Users\Administrator\Desktop\安装\arrest\1589653198108.png)

![58965323897](C:\Users\Administrator\Desktop\安装\arrest\1589653238974.png)

![58965326395](C:\Users\Administrator\Desktop\安装\arrest\1589653263958.png)

**需要按ctrl+F9从新加载**

## 8.Spring Boot与监控管理

![58965457570](C:\Users\ADMINI~1\AppData\Local\Temp\1589654575708.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1589659866603.png)

开启所有端点

![58966065619](C:\Users\Administrator\Desktop\安装\arrest\1589660656198.png)

**springboot admin**(可视化管理)

![58966121742](C:\Users\Administrator\Desktop\安装\arrest\1589661217428.png)

服务端

1.引入依赖，web启动器，修改spring boot版本至2.1.x

```
<!-- https://mvnrepository.com/artifact/de.codecentric/spring-boot-admin-starter-server -->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
    <version>2.1.6</version>
</dependency>
```

2.修改端口

![58966187183](C:\Users\Administrator\Desktop\安装\arrest\1589661871832.png)

3.在启动类添加注解

![58966199353](C:\Users\Administrator\Desktop\安装\arrest\1589661993537.png)

<http://localhost:9090/>

![58966211643](C:\Users\Administrator\Desktop\安装\arrest\1589662116432.png)

客户端

1.引入依赖

```
<!-- https://mvnrepository.com/artifact/de.codecentric/spring-boot-admin-starter-client -->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>2.1.6</version>
</dependency>

```

2.配置地址（spring.boot.admin.client.url: ）

![58966248093](C:\Users\Administrator\Desktop\安装\arrest\1589662480936.png)

![58966292202](C:\Users\Administrator\Desktop\安装\arrest\1589662922024.png)

## 9.springboot服务器端数据校验

无需引入依赖，web启动器已整合

![58965725427](C:\Users\Administrator\Desktop\安装\arrest\1589657254270.png)

### 9.1对实体对象校验

![58965736519](C:\Users\Administrator\Desktop\安装\arrest\1589657365198.png)



**@NotNull:对基本数据类型的对象类型做非空校验（Long,Integer,可以，int等不可以）**

**@NotBlank:对字符串类型做非空校验**

**@NotEmpty:对集合类型做非空校验**



![58965783845](C:\Users\Administrator\Desktop\安装\arrest\1589657838452.png)

### 9.2对controller进行数据校验

**@Validated:对对象做校验**

![58965804129](C:\Users\Administrator\Desktop\安装\arrest\1589658041296.png)



### 9.3自定义错误信息

![58965848876](C:\Users\Administrator\Desktop\安装\arrest\1589658488766.png)



### 9.4其他校验规则

![58965891307](C:\Users\Administrator\Desktop\安装\arrest\1589658913077.png)

### 9.5controller中其他参数校验

在controller上加@Validator

在参数上加校验规则，比如@NotBlank



## 10.springboot整合servlet三大组件

### 10.1servlet

```
@WebServlet(name = "FirstServlet",urlPatterns = "/servlet")
public class FirstServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("firstservlet-------------");

    }
}

```

在启动类添加@ServletComponentScan

```
@SpringBootApplication
@ServletComponentScan
public class SpringbootSpringmvcApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootSpringmvcApplication.class, args);
    }

}
```

### 10.2filter

```
@WebServlet(name = "FirstServlet",urlPatterns = "/servlet")
public class FirstServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("firstservlet-------------");

    }
}
```

### 10.3listener

```
@WebListener
public class FirstListener implements ServletContextListener {
    //servlet初始化调用
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ServletContextListener----------init");
    }
    //servlet销毁调用
    @Override
    public void contextDestroyed(ServletContextEvent sce) {

    }
}

```











## 11.springboot 与消息（RabbitMQ）

1.引入web和rabbitMQ的启动器

2.配置

![59023555833](C:\Users\Administrator\Desktop\安装\arrest\1590235558330.png)

3.测试

![59023568141](C:\Users\Administrator\Desktop\安装\arrest\1590235681410.png)

4.通过序列化方式，将数据转为json发送和接受

![59023693327](C:\Users\Administrator\Desktop\安装\arrest\1590236933274.png)

5.

![59023699994](C:\Users\Administrator\Desktop\安装\arrest\1590236999946.png)

6.

![59023733708](C:\Users\Administrator\Desktop\安装\arrest\1590237337082.png)

7.监听

![59023753000](C:\Users\Administrator\Desktop\安装\arrest\1590237530004.png)

8.通过代码创建quenue和exchange(amqpAdmin)

![59023788965](C:\Users\Administrator\Desktop\安装\arrest\1590237889650.png)

## 12.springboot整合elasticsearch

1.docker安装elasticsearch

### 简单入门

**postman测试**

2.增（**put请求**）

www.allweing.cn:9200/megacorp/employee/1

megacorp:索引（数据库）

employee:类型 （表）

文档：（每行数据）

```
{
    "first_name" : "John",
    "last_name" :  "Smith",
    "age" :        25,
    "about" :      "I love to go rock climbing",
    "interests": [ "sports", "music" ]
}
```

3.删（**delete请求**）

```
www.allweing.cn:9200/megacorp/employee/1
```

4.改（**put请求**）同增

5.查(**get请求**)

```
www.allweing.cn:9200/megacorp/employee/1
```

6查找所有

```
www.allweing.cn:9200/megacorp/employee/_search
```

7.有条件简单查询

```
www.allweing.cn:9200/megacorp/employee/_search?age=25
```

8有条件复杂查询（post请求）

```
post请求   www.allweing.cn:9200/megacorp/employee/_search

请求体
{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
```

9.更复杂的条件查询

```
post请求   www.allweing.cn:9200/megacorp/employee/_search

请求体
{
    "query" : {
        "bool": {
            "must": {
                "match" : {
                    "last_name" : "smith" 
                }
            },
            "filter": {
                "range" : {
                    "age" : { "gt" : 30 } 
                }
            }
        }
    }
}
```

等等等等。。。

[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)

### spring-data-elasticsearch

1.引入相关依赖web  elasticsearch启动器

2.

![59024066807](C:\Users\Administrator\Desktop\安装\arrest\1590240668077.png)

**（可能出现连接超时，修改版本适配，spring-data-elasticsearch 和elasticsearch ）**

![59024119712](C:\Users\Administrator\Desktop\安装\arrest\1590241197127.png)

3

Book:数据实体类，internet:实体类主键

![59024134074](C:\Users\Administrator\Desktop\安装\arrest\1590241340749.png)

4.

![59024147758](C:\Users\Administrator\Desktop\安装\arrest\1590241477585.png)

5.

![59024159436](C:\Users\Administrator\Desktop\安装\arrest\1590241594369.png)

## 13.springboot整合springsecurity(安全框架)

















## 14.springboot自定义启动器

### 14.1创建启动工程

allweing-spring-boot-starter（只做打包发布引入功能）

引入自动配置工程

```
<dependencies>
        <dependency>
            <groupId>cn.allweing.starter</groupId>
            <artifactId>allweing-spring-boot-starter-autoconfiguration</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>
```

### 14.2创建自动配置工程

引入依赖

```
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.allweing.starter</groupId>
    <artifactId>allweing-spring-boot-starter-autoconfiguration</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <dependencies>
        <!--所有starter基本配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
    </dependencies>
```

编写业务类

```
public class HelloService {


    HelloProperties helloProperties;

    public HelloProperties getHelloProperties() {
        return helloProperties;
    }

    public void setHelloProperties(HelloProperties helloProperties) {
        this.helloProperties = helloProperties;
    }
	//主要方法
    public String getName(String name) {
        return helloProperties.getPrefix()+name+helloProperties.getSuffix();
    }
}

```

编写属性读取类

```
@ConfigurationProperties(prefix = "cn.allweing")
public class HelloProperties {
   private String prefix;
   private String suffix;

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }
}

```

编写自动配置类

```
@Configuration
@ConditionalOnWebApplication
@EnableConfigurationProperties(HelloProperties.class)
public class HelloServiceAutoConfiguration {

    @Resource
    HelloProperties helloProperties;

    @Bean
    public HelloService helloService() {
        HelloService helloService = new HelloService();
        helloService.setHelloProperties(helloProperties);
        return helloService;
    }
}

```

在resources文件夹下创建META-INf文件夹

创建文件spring.factories

引入自动配置类

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  cn.allweing.starter.HelloServiceAutoConfiguration
```

![59125133819](C:\Users\Administrator\Desktop\安装\arrest\1591251338195.png)

### 14.3打包安装应用

先安装自动配置工程，在安装启动工程（install）

![59125144660](C:\Users\Administrator\Desktop\安装\arrest\1591251446601.png)

在其他工程中引入

![59125152114](C:\Users\Administrator\Desktop\安装\arrest\1591251521142.png)

配置文件

![59125155064](C:\Users\Administrator\Desktop\安装\arrest\1591251550644.png)

应用

![59125158957](C:\Users\Administrator\Desktop\安装\arrest\1591251589575.png)

