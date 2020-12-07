# mybaties-plus

## 1.入门

![](C:\Users\Administrator\Desktop\安装\arrest\1588508180413.png)

### 1.1创建数据库，创建表

![](C:\Users\Administrator\Desktop\安装\arrest\1588508342503.png)

```sql
DROP TABLE IF EXISTS user;

CREATE TABLE user
(
	id BIGINT(20) NOT NULL COMMENT '主键ID',
	name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
	age INT(11) NULL DEFAULT NULL COMMENT '年龄',
	email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (id)
);
```

```sql
DELETE FROM user;

INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');
```

### 1.2创建springboot工程，添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

    <!--mybatis-plus-->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.0.5</version>
    </dependency>

    <!--mysql-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>

    <!--lombok用来简化实体类-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
</dependencies>
```

### 1.3配置

```
#mysql数据库连接
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus
spring.datasource.username=root
spring.datasource.password=123456
```

```
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus?serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=123456
```

![](C:\Users\Administrator\Desktop\安装\arrest\1588509188004.png)

### 1.4编写代码

```
@Data
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

```
public interface UserMapper extends BaseMapper<User> {
    
}
```

```
@SpringBootApplication
@MapperScan("com.atguigu.mybatisplus.mapper")
public class MybatisPlusApplication {
	......
}
```

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class MybatisPlusApplicationTests {

    @Autowired
    private UserMapper userMapper;

    @Test
    public void testSelectList() {
        System.out.println(("----- selectAll method test ------"));
        //UserMapper 中的 selectList() 方法的参数为 MP 内置的条件封装器 Wrapper
        //所以不填写就是无任何条件
        List<User> users = userMapper.selectList(null);
        users.forEach(System.out::println);
    }
}
```

### 1.5异常

![58851145142](C:\Users\Administrator\Desktop\安装\arrest\1588511451420.png)

```
 <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-launcher</artifactId>
            <scope>test</scope>
        </dependency>
```

### 1.6配置日志，查看sql

```
#mybatis日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

## 2.插入操作

```
 @Test
    public void add() {
        User user = new User();
        user.setAge(11);
        user.setEmail("1589953744@qq.com");
        user.setName("zzzgy");

        int i = userMapper.insert(user);
        System.out.println(i);
    }
```

![](C:\Users\Administrator\Desktop\安装\arrest\1588514995646.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588515534442.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588515594074.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588515711986.png)

## 3.修改操作

```
 @Test
    public void updata() {
        User user = new User();
        user.setId(1l);
        user.setAge(27);

        int i = userMapper.updateById(user);
        System.out.println(i);
    }
```

### 3.1自动填充

项目中经常会遇到一些数据，每次都使用相同的方式填充，例如记录的创建时间，更新时间等。

我们可以使用MyBatis Plus的自动填充功能，完成这些字段的赋值工作：

1.在User表中添加datetime类型的新的字段 create_time、update_time

2实体上添加注解(**字段是驼峰式**)

```java
@Data
2
public class User {
3
    ...
4
        
5
    @TableField(fill = FieldFill.INSERT)
6
    private Date createTime;
7
8
    //@TableField(fill = FieldFill.UPDATE)
9
    @TableField(fill = FieldFill.INSERT_UPDATE)
10
    private Date updateTime;
11
}
```

3.实现元对象处理器接口注意：不要忘记添加 @Component 注解

```
@Component
10
public class MyMetaObjectHandler implements MetaObjectHandler {
11
12
    private static final Logger LOGGER = LoggerFactory.getLogger(MyMetaObjectHandler.class);
13
14
    @Override
15
    public void insertFill(MetaObject metaObject) {
16
        LOGGER.info("start insert fill ....");
17
        this.setFieldValByName("createTime", new Date(), metaObject);
18
        this.setFieldValByName("updateTime", new Date(), metaObject);
19
    }
20
21
    @Override
22
    public void updateFill(MetaObject metaObject) {
23
        LOGGER.info("start update fill ....");
24
        this.setFieldValByName("updateTime", new Date(), metaObject);
25
    }
26
}
```

### 3.2乐观锁

![](C:\Users\Administrator\Desktop\安装\arrest\1588518943723.png)

1.主要适用场景：当要更新一条记录的时候，希望这条记录没有被别人更新，也就是说实现线程安全的数据更新

![](C:\Users\Administrator\Desktop\安装\arrest\1588559122328.png)

2.实现原理

![](C:\Users\Administrator\Desktop\安装\arrest\1588559261617.png)

3.代码

```

@Version
@TableField(fill = FieldFill.INSERT)
private Integer version;
```

元对象处理器接口添加version的insert默认值

![](C:\Users\Administrator\Desktop\安装\arrest\1588559422899.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588559462851.png)

在 MybatisPlusConfig 中注册 Bean

![](C:\Users\Administrator\Desktop\安装\arrest\1588559511180.png)

测试乐观锁可以修改成功

![](C:\Users\Administrator\Desktop\安装\arrest\1588559573106.png)

测试乐观锁修改失败

![](C:\Users\Administrator\Desktop\安装\arrest\1588559611095.png)

## 4.查询操作

单个查询

```
@Test
    public void selectById() {

        User user = userMapper.selectById(1l);
        System.out.println(user);

    }
```

批量查询

```
 @Test
    public void selectByIds() {
        List<User> users = userMapper.selectBatchIds(Arrays.asList(1l, 2l, 3l));
        users.forEach(System.out::println);
    }
```

简单条件查询（**注意：map中的key对应的是数据库中的列名。例如数据库user_id，实体类是userId，这时map的key需要填写user_id**）

```
 @Test
    public void testSelectByMap(){


        HashMap<String, Object> map = new HashMap<>();

        map.put("name", "jack");

        map.put("age", 20);

        List<User> users = userMapper.selectByMap(map);
        users.forEach(System.out::println);

    }
```

### 4.1分页查询

1.分页插件

```
/**
     * 分页插件
     */

    @Bean
    public PaginationInterceptor paginationInterceptor() {
        return new PaginationInterceptor();
    }
```

2.编写代码

```
 @Test
    public void testPage() {
        Page<User> page = new Page<>(1, 3);

        //底层将所有的数据被封装到Page对象中
        IPage<User> userIPage = userMapper.selectPage(page, null);

        System.out.println(userIPage.getCurrent());
        System.out.println(userIPage.getRecords());
        System.out.println(page.getTotal());
        System.out.println(page == userIPage);
    }
```

## 5.删除操作

1.根据id删除

```
  @Test
    public void testDeleteById() {
        int i = userMapper.deleteById(1l);
        System.out.println(i);
    }
```

2.批量删除

```
@Test
    public void testDeleteByIds() {
        int i = userMapper.deleteBatchIds(Arrays.asList(2l, 3l, 4l));
        System.out.println(i);
    }
```

3.简单条件删除

```

    @Test
    public void testDeleteByMap() {
        Map<String, Object> map = new HashMap<>();
        map.put("name","Billie");
        map.put("age", 24);

        int i = userMapper.deleteByMap(map);
        System.out.println(i);
    }
```

### 5.1逻辑删除

- 物理删除：真实删除，将对应数据从数据库中删除，之后查询不到此条被删除数据
- 逻辑删除：假删除，将对应数据中代表是否被删除字段状态修改为“被删除状态”，之后在数据库中仍旧能看到此条数据记录

![](C:\Users\Administrator\Desktop\安装\arrest\1588659248299.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588659286473.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588659320545.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588659356107.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588659398517.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588659425956.png)

## 6.性能分析

性能分析拦截器，用于输出每条 SQL 语句及其执行时间

SQL 性能执行分析,开发环境使用，超过指定时间，停止运行。有助于发现问题

```
 @Bean
    @Profile({"dev","test"})// 设置 dev test 环境开启
    public PerformanceInterceptor performanceInterceptor() {

        PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
        performanceInterceptor.setMaxTime(100);//ms，超过此处设置的ms则sql不执行
        performanceInterceptor.setFormat(true);
        return performanceInterceptor;
    }
```

Spring Boot 中设置dev环境

```
1
#环境设置：dev、test、prod
2
spring.profiles.active=dev
```

## 7.复杂查询

![](C:\Users\Administrator\Desktop\安装\arrest\1588662601345.png)

![](C:\Users\Administrator\Desktop\安装\arrest\fuzha.jpg)

## 8.配置SQL执行性能分析插件

创建config包，创建MyBatisPlusConfig.java

```
@Configuration
@EnableTransactionManagement
@MapperScan("com.atguigu.eduservice.mapper")
public class MyBatisPlusConfig {
    
}
```

```
@Bean
@Profile({"dev","test"})// 设置 dev test 环境开启
public PerformanceInterceptor performanceInterceptor() {
    PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
    performanceInterceptor.setMaxTime(1000);//ms，超过此处设置的ms则sql不执行
    performanceInterceptor.setFormat(true);
    return performanceInterceptor;
}
```

## 9.其他

默认情况下json时间格式带有时区，并且是世界标准时间，和我们的时间差了八个小时
在application.properties中设置

```
#返回json的全局时间格式
spring.jackson.date-format=yyyy-MM-dd HH:mm:ss
spring.jackson.time-zone=GMT+8
```

# mybaties

