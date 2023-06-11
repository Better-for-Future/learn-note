# docker 常用命令（run 篇）

命令格式：**docker run [OPTIONS] IMAGE [COMMAND] [ARG…]**

Usage: Run a command in a new container
用法：通过run命令创建一个新的容器（container）

- 常用选项说明

```
-d, --detach： 指定容器在后台运行，默认为 false。
-e, --env： 指定环境变量。
-i：以交互模式运行容器，通常与 -t 同时使用；
-t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；
-p，--publish：指定容器暴露的端口，宿主端口:容器内部端口
--name：为容器指定一个名字。
-v,--volume: 映射目录（卷），宿主目录:容器内部目录，常用来将配置文件或者是数据文件放在宿主机器上。
--restart: 指定容器停止后的重启策略：
     no: 容器退出时不重启，默认值
     no-failure: 容器故障退出（返回值为零）时重启，no-failure:3 循环重启3次。
     always: 容器退出时总是重启
     unless-stopped: 重新启动容器，除非它被显式停止或Docker本身被停止或重新启动。
--privileged：指定容器是否为特权容器，特权容器拥有所有的权限，默认为 false。
--rm：指定容器停止后自动删除容器(不支持以docker run -d 后天启动的容器)，默认为 false.
```

- 示例

```
docker run xxx：运行应用程序。例如运行用来测试 Docker 是否安装成功的简单镜像：docker run hello-world。
docker run --detach --publish=80:80 --name=webserver nginx：后台运行一个 Nginx 容器，端口为 80，容器名为 webserver
docker run -d -p 80:80 --name weberver nginx: 简写形式，同以上命令。
```



[修改 Docker 中 MySQL 8 容器时区的问题](http://roc.havemail.cn/archives/1232.html?relatedposts_hit=1&relatedposts_origin=355&relatedposts_position=0)

[Docker 一分钟快速运行 Centos](http://roc.havemail.cn/archives/877.html?relatedposts_hit=1&relatedposts_origin=355&relatedposts_position=1)

[详解 Docker 的三种网络代理配置](http://roc.havemail.cn/archives/1640.html?relatedposts_hit=1&relatedposts_origin=355&relatedposts_position=2)