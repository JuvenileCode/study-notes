## Spring 读取配置文件

[参考来源](https://juejin.cn/post/6844904061888643085)

```yml
my-profile:
  name: God
  email: god@gmail.com

library:
  location: 万事万物都不可能是永恒的
  books:
    - name: 天才基本法
      description: 二十二岁
    - name: 时间的秩序
      description: 为什么我们记得过去
    - name: 了不起的我
      description: 如何养成一个新习惯
```

### @Value

```java
// @value这种方式是不被推荐的
@Value("${my-profile.email}")
private String email;
```

### @ConfigurationProperties读取并与 bean 绑定

**定义：**

```java
@Component
@ConfigurationProperties(prefix = "library")
@Setter
@Getter
@ToString
class LibraryProperties {
    private String location;
    private List<Book> books;

    @Setter
    @Getter
    @ToString
    static class Book {
        String name;
        String description;
    }
}
```

**使用：**

```java
@SpringBootApplication
public class ReadConfigPropertiesApplication implements InitializingBean {

    private final LibraryProperties library;

    public ReadConfigPropertiesApplication(LibraryProperties library) {
        this.library = library;
    }

    public static void main(String[] args) {
        SpringApplication.run(ReadConfigPropertiesApplication.class, args);
    }

    @Override
    public void afterPropertiesSet() {
        System.out.println(library.getLocation());
        System.out.println(library.getBooks());    }
}
```

### @ConfigurationProperties读取并校验

```yaml
my-profile:
  name: God
  email: god@aadfl
```

**定义：**

```java
@Getter
@Setter
@ToString
@ConfigurationProperties("my-profile")
@Validated
public class ProfileProperties {
   @NotEmpty
   private String name;

   @Email
   @NotEmpty
   private String email;

   //配置文件中没有读取到的话就用默认值
   private Boolean handsome = Boolean.TRUE;

}
```

**使用：**

```java
@SpringBootApplication
@EnableConfigurationProperties(ProfileProperties.class)
public class ReadConfigPropertiesApplication implements InitializingBean {
    private final ProfileProperties profileProperties;

    public ReadConfigPropertiesApplication(ProfileProperties profileProperties) {
        this.profileProperties = profileProperties;
    }

    public static void main(String[] args) {
        SpringApplication.run(ReadConfigPropertiesApplication.class, args);
    }

    @Override
    public void afterPropertiesSet() {
        System.out.println(profileProperties.toString());
    }
}
```

```java
// 运行结果 - 邮箱格式不正确，所以程序运行的时候就报错
Binding to target org.springframework.boot.context.properties.bind.BindException: Failed to bind properties under 'my-profile' to cn.javaguide.readconfigproperties.ProfileProperties failed:

    Property: my-profile.email
    Value: koushuangbwcx@
    Origin: class path resource [application.yml]:5:10
    Reason: must be a well-formed email address
```

### @PropertySource读取指定 properties 文件

**定义：**

```java
@Component
@PropertySource("classpath:website.properties")
@Getter
@Setter
class WebSite {
    @Value("${url}")
    private String url;
}
```

**使用：**

```java
@Autowired
private WebSite webSite;

System.out.println(webSite.getUrl());//https://javaguide.cn/
```

