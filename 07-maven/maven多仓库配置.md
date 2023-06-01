# Maven多仓库配置

Black Gold

已于 2023-04-06 14:29:30 修改

208
 收藏 2
文章标签： maven java intellij-idea
版权
有两种方式配置Maven多仓库
setting.xml文件的profiles标签
pom.xml文件的repositories标签
在使用多仓库配置时，不管使用哪种方式，必须先将setting.xml文件中的mirrors标签修改为如下内容（如果有mirrors标签），关键是mirrorOf千万不要写成

```xml
*，否则多仓库配置不生效
  <mirrors>
    <!-- mirrorOf不要写*号，否则所有的请求都会使用此地址，此处仅仅代理默认的中央仓库 -->
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>https://maven.aliyun.com/repository/public</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
```


两种方式可以同时使用，setting.xml方式优先级高于pom.xml

1、setting.xml文件的profiles标签
如何配置
在setting.xml文件的settings标签中找到profiles标签（如果没有请直接新增），将其替换成如下配置（具体配置取决于自身需要的仓库）

  

```xml
<profiles>
<!-- 有赞仓库 -->
<profile>
  <id>Youzanyun</id>
  <repositories>
    <repository>
      <id>youzanyun-central</id>
      <name>Youzan Central Repository</name>
      <url>http://maven.youzanyun.com/repository/maven-central/</url>
      <releases>
        <enabled>true</enabled>
      </releases>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
    <repository>
      <id>youzanyun-releases</id>
      <name>Youzan Release Repository</name>
      <url>http://maven.youzanyun.com/repository/maven-releases/</url>
      <releases>
        <enabled>true</enabled>
      </releases>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>
</profile>

<!-- 阿里仓库 -->
<profile>
  <id>Aliyun</id>
  <repositories>
    <repository>
      <id>aliyun-public</id>
      <name>Aliyun Public Repository</name>
      <url>https://maven.aliyun.com/repository/public</url>
      <releases>
        <enabled>true</enabled>
      </releases>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
    <repository>
      <id>aliyun-spring</id>
      <name>Aliyun Spring Proxy Repository</name>
      <url>https://maven.aliyun.com/repository/spring</url>
      <releases>
        <enabled>true</enabled>
      </releases>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
    </repository>
  </repositories>
</profile>
</profiles>

<activeProfiles>
    <activeProfile>Youzanyun</activeProfile>
    <activeProfile>Aliyun</activeProfile>
</activeProfiles>
```



profiles中可以有多个profile，每个profile有一个唯一的ID，在每个profile中可以指定多个repository，repository用于指定仓库地址、ID、名称等信息，建议每个仓库的id不要重复，有利于查看jar包来源。

如何使配置生效
上面配置完毕并不会生效，有两种简单的方式可以使其生效

1、在setting.xml文件的settings标签中找到activeProfiles标签（如果没有请直接新增），将其替换成如下配置（activeProfile标签中的值写配置好的profile的id）

```xml
   <activeProfiles>
    <activeProfile>Youzanyun</activeProfile>
    <activeProfile>Aliyun</activeProfile>
  </activeProfiles>
```


2、在IDEA中手动勾选，IDEA右上角找到并打开Maven标签页，展开Profiles，勾选上需要的profile

需要哪个profile生效就勾选哪个，注意：如果已经在activeProfiles中配置了某个profile，在此处取消勾选是不起作用的
2、pom.xml文件的repositories标签
这种方式就简单得多，直接打开项目的pom.xml文件，在project标签下增加如下配置即可

```xml
<repositories>
    <repository>
        <id>youzanyun-central-pom</id>
        <name>Nexus Release Repository</name>
        <url>http://maven.youzanyun.com/repository/maven-central1/</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
    <repository>
        <id>youzanyun-releases-pom</id>
        <name>Nexus Release Repository</name>
        <url>http://maven.youzanyun.com/repository/maven-releases1/</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

优先级
当同时使用setting.xml方式和pom.xml方式，setting.xml方式优先级高于pom.xml。

setting.xml中多个profiles优先级
profiles如果存在多个profile，优先级与profile在文件中声明的顺序有关，越靠后优先级越高。

setting.xml中多个repository优先级
在每个profile中有个repositorys标签，在repositorys中可以指定多个repository，多个repository的优先级与其声明的顺序有关，越靠前优先级越高，这与多个profile的优先级刚好相反。

pom.xml中多个repository优先级
在每个pom.xml文件中也有个repositorys标签，在repositorys中可以指定多个repository，多个repository的优先级与其声明的顺序有关，越靠前优先级越高。

总结
从远程仓库获取jar包过程中，如果配置了多个仓库，那么仓库的生效顺序如下：

从setting.xml中获取激活的profile，多个profile按照声明顺序倒序排列，依次处理每一个profile（激活的）；
在单个profile中按照repository声明顺序依次查找，直到找到需要的jar包，如果没找到就到下一个profile中查找；
所有的profile配置的仓库都没找到，那就需要查找pom.xml中配置的仓库，按照repository在pom.xml文件中声明的顺序依次查找，如果还没有，jar包下载失败；
————————————————
版权声明：本文为CSDN博主「Black Gold」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_37624120/article/details/129987312