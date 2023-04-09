Maven多模块下pom依赖关系解读

静笃塾

于 2022-10-25 22:24:16 发布

545
 收藏
分类专栏： maven Java 编程基础 文章标签： spring boot spring java
版权

maven
同时被 3 个专栏收录
2 篇文章0 订阅
订阅专栏

Java
11 篇文章0 订阅
订阅专栏

编程基础
12 篇文章0 订阅
订阅专栏
 Maven采取多模块进行开发，父子模块的传递关系，及各个参数的说明

包括：

1 父子模块的传递

2 依赖关系 版本管理

3 各个属性的说明

建议不用过分强调父模块、子模块，只是取名子。父模块可以是子模块，子模块可以是父模块。都一样，只是里面的参数设置不同罢了，如果仔细写Pom,相同也没啥。



```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
<!--GAV三项,必须要有,进行唯一性锁定,不然都找不到具体的jar包-->
<!--当前项目或模块的工程id-->
<groupId>bob.spring</groupId>
<!--groupID下面的子模块id-->
<artifactId>spring-boot-cloud</artifactId>
<!--具体模块的版本号-->
<version>1.0-SNAPSHOT</version>
<!--打包方式,父模块是Pom,子模块根据业务需要选择jar,war之类-->
<packaging>pom</packaging>
<!--取个名字,私以为可有可无-->
<name>${project.artifactId}</name>
 
<!--下述引入父模块,此处引入的是springboot,也可以引入自己编写的父模块-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.6.RELEASE</version>
</parent>
 
<!--该父模块下的所有子模块-->
<!--编译时,会按照顺序编译,能够解决依赖关系-->
<modules>
    <module>config</module>
    <module>serviceB</module>
    <module>serviceA</module>
</modules>
 
<!--属性名,我理解为宏定义,统一命名,其他模块可以使用,很方便统一管理版本-->
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>1.8</java.version>
    <docker.plugin.version>1.0.0</docker.plugin.version>
    <docker.image.prefix>spring-boot-cloud</docker.image.prefix>
    <spring.cloud.version>Dalston.SR4</spring.cloud.version>
    <spring-boot-admin.version>1.5.4</spring-boot-admin.version>
</properties>
 
<!--dependencies下的依赖会强制传递,即子模块一定会继承-->
<!--建议把一定会用,最常用的依赖写入；都写,小心依赖错误-->
 
<!--如果想引入其他子模块的功能,如在上述modules里，serviceA引入serviceB-->
<!--则在serviceA的pom里，在此处引入serviceB的GAV-->
<!--如果是springbootapplication,还需要改动扫描路径，不然没法自动装配-->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
        <!--此处没有写versionid,因为直接继承了父模块的,很方便版本统一管理-->
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
 
<!--dependencyManagement下的依赖，子模块可以自由选择-->
<!--若子模块引入相应依赖,没有加version,则表明直接继承自父依赖-->
<!--若子模块引入相应依赖,加了version,则使用自己的version。有点像作用域关系-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-server</artifactId>
            <version>${spring-boot-admin.version}</version>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-server-ui</artifactId>
            <version>${spring-boot-admin.version}</version>
        </dependency>
    </dependencies>
</dependencyManagement>
 
<!--pluginmanagement的功能与dependencymanagement基本一致，都是为了管理父子模块的依赖传递-->
<build>
    <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
 
        </plugins>
    </pluginManagement>
</build>
</project>
```

参考链接

[maven pom](https://blog.csdn.net/TNT_D/article/details/105081988)

[maven pom another](https://www.jianshu.com/p/38a41b3b9862)


