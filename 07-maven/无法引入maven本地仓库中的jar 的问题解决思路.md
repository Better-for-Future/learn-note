# [无法引入maven本地仓库中的jar 的问题解决思路](https://blog.csdn.net/donkeyboy001/article/details/119573617)



maven 仓库（reporsitory）中已存在此jar 包，并且坐标正确，但引入时这个包没有引入，并且出现更新异常的标志 .lastUpdated

1.原因：可能是配置本地仓库配置错误



发现配置正确，查看maven 的setting 中的配置的repository 也是正确的

2.配置的仓库是否未更新，更新一下还是不行



3.是忽略的pom.xml 文件，查看也未忽略，说明pom 文件正常解析



4.突然想到无法加载的包依赖是别人从私服（nexus 中搞下来的），查看仓库中这个包的 _remote.repositories 文件

#NOTE: This is an Aether internal implementation file, its format can be changed without prior notice.
#Wed Oct 30 11:19:07 CST 2019
itextpdf-5.2.0.pom>nexus=
itextpdf-5.2.0.jar>alimaven=
itextpdf-5.2.0.jar>nexus=
itextpdf-5.2.0.pom>alimaven=
>nexus 说明这个依赖是从私服中下载下来的。而此时我是连接连接不上私服的

对比正常下载的包的_remote.repositories

#NOTE: This is an Aether internal implementation file, its format can be changed without prior notice.
#Sat Sep 08 15:25:53 CST 2018
druid-1.0.11.jar>central=
druid-1.0.11.pom>central=
这时出错的问题就很明显了，把_remote.repositories 中的nexus  改为central  重新导入就可以了。

总结：遇到问题，沉着冷静，一定能找到问题的结果。