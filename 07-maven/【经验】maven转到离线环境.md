【经验】maven转到离线环境

吃饱饭长的快

于 2022-03-31 10:09:53 发布

5959
 收藏 6
文章标签： maven eclipse idea java spring
版权
----本文的适用场景为，在有网络环境下编写的maven工程，移植到无外网及无maven仓库环境下（下文简称内网环境），致力于解决初学者或者不关注开发环境本身的开发者问题。

    之前有发现同事直接把外网整个maven仓库覆盖到内网环境使用，此方法没啥问题，但是对于仓库的简洁和使用不方便，比如我只想在内网引用spring-boot-starter-jdbc，全量覆盖就太奢侈了



【开发工具】idea、eclipse

【中间件】maven

【框架】不限制

直接开始：

1. 将外网下载完成的本地maven库打包，扔到内网环境。本地maven仓库位置查看方式如下：
【eclipse】windows-Preferences-Maven-User Settings-Local Repository**



【idea】File-Settings-Build,Execution,Deployment-Build Tools-Maven



如果上述没有进行配置，则使用的maven默认的路径，

windows下是在C:\Users\Administrator\.m2\repository

2.外网maven包放到内网环境,修改内网maven配置
【存放全量maven】把外网的maven打包放到内网`D:\maven_repo\all`目录下

【修改maven配置】找到内网环境的maven中间件，修改setting文件，如D:\soft2\apache-maven-3.6.3\conf\settings.xml，

配置本地maven仓库路径：

D:\soft2\apache-maven-3.6.3\conf\settings.xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository  -->
  <localRepository>D:\maven_repo\repository</localRepository>
...
配置仓库源地址，把本地`D:\maven_repo\all`作为仓库源：

  <mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |-->
    <mirror>
      <id>central</id>
      <name>central</name>
      <url>file://D:\maven_repo\all</url>
	  <mirrorOf>*</mirrorOf>
    </mirror>
     
  </mirrors>
【刷新开发工具】eclipse中可右键项目-Maven-Update Project，idea点击右侧的Maven，将拓展框左上角的三个按钮都点一下





【开发工具爆红】有时候刷新了开发工具，还是没有成功加载包，这时候就把项目移除，重新导入项目吧...

【拓展】将`D:\maven_repo\all`作为了全量的仓库源，后续可从外网无脑扔库到此路径下，项目中需要引用哪个只需要在pom中引用就好了，maven会自动将关联依赖加载到工程仓库中

第一篇分享文，希望大家多多指正
————————————————
版权声明：本文为CSDN博主「吃饱饭长的快」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_29942637/article/details/123862874