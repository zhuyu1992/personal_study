

### 1.创建工程

### 2.改pom,引入jar包

```xml
<dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.3.RELEASE</version>
 </dependency>
```

### 3.简单demo

#### 3.1

```java
public class HelloWorld {
    private String name;

    public void getName() {
        System.out.println("YOU MESSAGE"+"\n"+name);

    }

    public void setName(String name) {
        this.name = name;
    }
}

```

#### 3.2

```java
public class MainApp {

    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        HelloWorld obj =(HelloWorld) context.getBean("helloWorld");
        obj.getName();

    }
}

```

#### 3.3

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <bean id="helloWorld" class="cn.allweing.HelloWorld">
        <property name="name" value="Hello World!"/>
    </bean>

</beans>
```

### 4.IOC

#### 4.1bean的作用域

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <!--<bean id="helloWorld" class="cn.allweing.Helloworld" scope="singleton">
    </bean>-->
    <bean id="helloWorld" class="cn.allweing.Helloworld" scope="prototype">
    </bean>

</beans>
```



![58799322002](C:\Users\ADMINI~1\AppData\Local\Temp\1587993220025.png)

#### 4.2Spring bean 的生命周期

Bean的生命周期可以表达为：Bean的定义——Bean的初始化——Bean的使用——Bean的销毁

### 5.依赖注入(基于注解)

#### 5.1Spring 基于注解的配置，修改配置文件

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">

   <context:annotation-config/>
   <!-- bean definitions go here -->

</beans>
```

#### 5.2四个主要注解

![](C:\Users\Administrator\Desktop\安装\arrest\1587997992638.png)



#### 5.3@Autowired注解

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:annotation-config/>
    <!-- bean definitions go here -->

    <bean id="student" class="cn.allweing.Student"/>
    <bean id="say" class="cn.allweing.Say">
        <property name="name" value="zhuyu"/>
        <property name="age" value="12"/>
    </bean>
    
</beans>
```

```java
public class Student {

    @Autowired
    private Say say;

    public void speak() {
        say.getName();
    }
}
```

```java
public class Say {
    private String name;

    public String getName() {
        System.out.println(name);
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    private int age;
}

```

```java
public class MainApp {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student bean = context.getBean("student", Student.class);
        bean.speak();
    }
}

```

```
zhuyu
```

#### 5.4@Qualifier 注释

![](C:\Users\Administrator\Desktop\安装\arrest\1587999504556.png)

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:annotation-config/>
    <!-- bean definitions go here -->

    <bean id="student" class="cn.allweing.Student"/>
    <bean id="say" class="cn.allweing.Say">
        <property name="name" value="zhuyu"/>
        <property name="age" value="12"/>
    </bean>
     <bean id="say2" class="cn.allweing.Say">
        <property name="name" value="zhuyuguang"/>
        <property name="age" value="12"/>
    </bean>
</beans>
```



```java
public class Student {

    @Autowired
    @Qualifier("say2")
    private Say say;

    public void speak() {
        say.getName();
    }
}
```

#### 5.5Spring JSR-250 注释

![](C:\Users\Administrator\Desktop\安装\arrest\1587999701555.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1587999786917.png)

#### 5.6Spring 基于 Java 的配置（不需要beans.xml）

![](C:\Users\Administrator\Desktop\安装\arrest\1588000168083.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588000274049.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588000312771.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588000488533.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588000550886.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1588004567917.png)

### 6.Spring -AOP

#### 6.1Spring 框架的 AOP

![](C:\Users\Administrator\Desktop\安装\arrest\1588038533244.png)



![](C:\Users\Administrator\Desktop\安装\arrest\1588038572959.png)

#### 6.2Spring 中基于 AOP 的 XML架构

```xml
<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.5</version>
        </dependency>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

    <aop:config>
        <aop:aspect id="log" ref="logging">
            <aop:pointcut id="selectAll"
                          expression="execution(* cn.allweing.*.*(..))"/>
            <aop:before pointcut-ref="selectAll" method="beforeAdvice"/>
            <aop:after pointcut-ref="selectAll" method="afterAdvice"/>
            <aop:after-returning pointcut-ref="selectAll"
                                 returning="retVal"
                                 method="afterReturningAdvice"/>
            <aop:after-throwing pointcut-ref="selectAll"
                                throwing="ex"
                                method="AfterThrowingAdvice"/>
        </aop:aspect>
    </aop:config>

    <!-- Definition for student bean -->
    <bean id="student" class="cn.allweing.Student">
        <property name="name" value="Zara"/>
        <property name="age" value="11"/>
    </bean>

    <!-- Definition for logging aspect -->
    <bean id="logging" class="cn.allweing.Logging"/>

</beans>
```

```java
public class Student {
    private Integer age;
    private String name;

    public void setAge(Integer age) {
        this.age = age;
    }

    public Integer getAge() {
        System.out.println("Age : " + age);
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        System.out.println("Name : " + name);
        return name;
    }
    
}

```

```java
public class Logging {

    public void beforeAdvice(){
        System.out.println("Going to setup student profile.");
    }
    /**
     * This is the method which I would like to execute
     * after a selected method execution.
     */
    public void afterAdvice(){
        System.out.println("Student profile has been setup.");
    }
    /**
     * This is the method which I would like to execute
     * when any method returns.
     */
    public void afterReturningAdvice(Object retVal){
        System.out.println("Returning:" + retVal.toString() );
    }
    /**
     * This is the method which I would like to execute
     * if there is an exception raised.
     */
    public void AfterThrowingAdvice(IllegalArgumentException ex){
        System.out.println("There has been an exception: " + ex.toString());
    }
}
```

```java
public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        Student student = (Student) context.getBean("student");
        student.getName();
        student.getAge();
    }
}

```

####6.3Spring 中基于 AOP 的 注解架构

```
<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.5</version>
        </dependency>
```

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

    <aop:aspectj-autoproxy/>

    <bean id="logging" class="cn.allweing.Logging"/>
    <bean id="student" class="cn.allweing.Student"/>

</beans>
```

```
public class Student {

    public void say() {
        System.out.println("cn.allweing.Student***********say******");
    }

    public void speak() {
        System.out.println("cn.allweing.Student***********speak*****");
    }
}

```

```
@Aspect
public class Logging {

    @Pointcut("execution(* cn.allweing.*.*(..))")
    public void all() {

    }
    @Before("all()")
    public void beforAdvice() {
        System.out.println("cn.allweing.Logging*************beforAdvice*********");
    }
    @After( "all()")
    public void AfterAdvice() {
        System.out.println("cn.allweing.Logging**************AfterAdvice********");
    }

}

```

```
public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        Student student = context.getBean("student",Student.class);
        student.speak();
        student.say();

    }
}

```

