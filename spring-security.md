# springboot-security(安全)

## 1springboot-security认证和授权

### 1.1引入依赖

```
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
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
    </dependencies>
```



### 1.2编写spring-security的配置类

```
@EnableWebSecurity
public class MySecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //定制请求的授权规则
        http.authorizeRequests().antMatchers("/").permitAll()
                                .antMatchers("/level1/**").hasRole("VIP1")
                                .antMatchers("/level2/**").hasRole("VIP2")
                                .antMatchers("/level3/**").hasRole("VIP3");
        //开启自动配置的登陆功能，没有权限，跳到登陆页面
        http.formLogin();
        //注销
        http.logout();
    }
    //定义认证规则
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {

        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder()).withUser("zhang").password("123456").roles("VIP1")
                                     .and()
                                     .withUser("zhu").password(new BCryptPasswordEncoder().encode("123456")).roles("VIP2")
                                     .and()
                                     .withUser("yu").password(new BCryptPasswordEncoder().encode("123456")).roles("VIP3")
                                     .and()
                                     .withUser("all").password(new BCryptPasswordEncoder().encode("123456")).roles("VIP1","VIP2","VIP3");
    }
}
```

```
@Controller
public class DemoController {

    @GetMapping("/")
    public String getWelcome() {
        return "welcome";
    }

    @GetMapping("level1/{id}")
    public String getLevel1(@PathVariable("id") Long id) {
        return "level1/"+id;
    }
    @GetMapping("level2/{id}")
    public String getLevel2(@PathVariable("id") Long id) {
        return "level2/"+id;
    }
    @GetMapping("level3/{id}")
    public String getLevel3(@PathVariable("id") Long id) {
        return "level3/"+id;
    }
}

```

![](C:\Users\Administrator\Desktop\安装\arrest\1588081508715.png)

## 2权限控制

