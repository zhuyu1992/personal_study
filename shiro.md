# shiro

## 1.简介

### 2.案例

#### 2.1引入依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring-boot-web-starter</artifactId>
            <version>1.4.1</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

#### 2.2配置

```
@Configuration
public class ShiroConfig {

    //3.ShiroFilterFactorybean
    @Bean(name = "shiroFilterFactoryBean")
    public ShiroFilterFactoryBean getShiroFilterFactoryBean( @Qualifier("defaultWebSecurityManager") DefaultWebSecurityManager defaultWebSecurityManager ) {
        ShiroFilterFactoryBean factoryBean = new ShiroFilterFactoryBean();
        //设置安全管理器
        factoryBean.setSecurityManager(defaultWebSecurityManager);
        return factoryBean;
    }

    //2.DefaultWebSecurityManager
    @Bean
    public DefaultWebSecurityManager defaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(userRealm);

        return securityManager;
    }

    //1.创建reaml对象，需自定义

    @Bean
    public UserRealm userRealm() {
        return new UserRealm();
    }
}
```

```
public class UserRealm extends AuthorizingRealm {
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了doGetAuthorizationInfo");
        return null;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了doGetAuthenticationInfo");
        return null;
    }
}

```

```
@Controller
public class MyController {

    @GetMapping({"/","/index"})
    public String toShiro(Model model) {

        model.addAttribute("msg","hello world");
        return "index";
    }
    @GetMapping("/user/add")
    public String getAdd() {
        return "user/add";
    }
    @GetMapping("/user/update")
    public String getUpdate() {
        return "user/update";
    }
}

```

![](C:\Users\Administrator\Desktop\安装\arrest\1588209838074.png)

