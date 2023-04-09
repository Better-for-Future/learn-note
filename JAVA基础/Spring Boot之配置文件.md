[Spring Boot之配置文件](https://blog.csdn.net/weixin_46061449/article/details/126809850)

秋至十二

于 2022-09-11 22:06:35 发布

789
 收藏 2
文章标签： java spring spring boot
版权
配置文件在项目开发中是必不可少的，它有很多的使用方法，下面就配置文件的一些用法详情。

1. Spring Boot 配置文件加载优先级

        有两种配置文件 properties 和 yml，它们最大的区别就是格式不一样，而在同一目录下properties 的优先级是高于yml；

Spring Boot读取优先级：

        a. 项目根目录下的/config目录下的配置文件。
        b. 项目根目录下的配置文件。
        c. 项目类路径（resources）下的/config目录下的配置文件。
        d. 项目类路径（resources）下的配置文件。

bootstrap和application的优先级
        bootstrap配置文件由spring父上下文加载，并且比application配置文件优先加载（父上下文不会使用application配置文件），而application配置文件由子上下文加载。bootstrap加载的配置信息不能被application的相同配置覆盖。

注意：如果代码中有使用到配置文件中的参数，这时bootstrap和application都有这个参数，那么会优先使用application的参数。

2. Spring Boot 读取配置文件

在项目开发过程中，如果有多套环境，这时我们就不能把代码里面的参数写死，不然每次换环境测试的时候都需要改代码。这时我们就可以把不同环境的变量放到配置文件中，然后从配置文件中获取参数。

a. @Value   

非静态参数：一般情况下可以使用@value直接获取配置文件中的参数。

server.port=8090

@Value("${server.port}")
private String serverPort;
静态参数：当类中的静态方法需要从配置文件读取参数时，需要将参数定义为静态，才可以在静态方法中获取到，这时候就不能只配置个@Value注解就行了。

server.port=8090

@Component
public class Text {

    private static String serverPort;
     
    @Value("${server.port}")
    public void setServerPort(String serverPort){
        Text.serverPort = serverPort;
    }

}
b. @ConfigurationProperties
@ConfigurationProperties 和 @Value 的使用是不相同的，@Value一次只可以读取一个配置参数，而@ConfigurationProperties 可以读取一组配置参数；

text.name=zan
text.sex=nan

@Component
@ConfigurationProperties(prefix = "text", ignoreUnknownFields = true)
public class ConfigurationPropertiesConfig {

    private String name;
     
    private String sex;
     
    public String getName() {
        return name;
    }
     
    public void setName(String name) {
        this.name = name;
    }
     
    public String getSex() {
        return sex;
    }
     
    public void setSex(String sex) {
        this.sex = sex;
    }

}
c. Environment
Environment 是 Spring Core 中的一个用于读取配置文件的类，将此类使用 @Autowired 注入到类中就可以使用它的 getProperty 方法来获取某个配置项的值了。

server.port=8090

@RestController
@RequestMapping("/text")
public class Text {

    @Autowired
    private Environment environment;
     
    @GetMapping("get")
    public String text(){
        return environment.getProperty("server.port");
    }
}
d. @PropertySource
可以指定读取的配置文件

server.port=8090

@Component
@PropertySource("classpath:application.properties")
public class Text {
    @Value("${server.port}")
    private String serverPort;
}
e. 使用原生方式读取配置文件
当有些场景不能使用注入的形式去获取配置文件中的参数时，可以使用PropertiesLoaderUtils这个工具类去获取到配置文件中的参数。

@RestController
@RequestMapping("/text")
public class Text {

    @GetMapping("get")
    public String text(){
        try {
            return PropertiesLoaderUtils.
                            loadAllProperties("application.properties").get("server.port").toString();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return "";
    }
}
3. 其他

配置文件中获取环境变量

在配置文件中，我们只需要使用${变量名}就可以声明一个使用环境变量的值。如果想向这个值声明一个默认值，以确保未在环境变量中找到此值时不会出错，使用${变量名:默认值}即可。

liunx中环境变量  /etc/profile 
export HOST=127.0.0.1

application.properties

host=${HOST:8080}
日志中使用配置文件的参数

当我们在实际开发中，如果遇到日志需要上传到阿里云，而且不同的环境 accessKeyId 和 accessKeySecret 都不同，这时我们就可以根据配置文件来进行区分，可以在日志.xml中获取到配置文件中的参数；

application.properties

logback.accessKeyId=111
logback.accessKeySecret=222
logback.project=aliyun
logback.logStore=aliyun-1


日志.xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="false">

    <springProperty scope="context" name="project" source="logback.project"/>
    <springProperty scope="context" name="logStore" source="logback.logStore"/>
    <springProperty scope="context" name="accessKeyId" source="logback.accessKeyId"/>
    <springProperty scope="context" name="accessKeySecret" source="logback.accessKeySecret"/>

</configuration>
文章主要是在实际开发中有使用的场景，并且做个记录，方面下次使用时查阅。
————————————————
版权声明：本文为CSDN博主「秋至十二」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_46061449/article/details/126809850