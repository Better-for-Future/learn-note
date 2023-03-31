# mysql主从复制安装配置

### 1、基础设置准备

```shell
#操作系统：
centos6.5
#mysql版本：
5.7
#两台虚拟机：
node1:192.168.85.111（主）
node2:192.168.85.112（从）
```

### 2、安装mysql数据库

```shell
#详细安装和卸载的步骤参考对应的文档
```

### 3、在两台数据库中分别创建数据库

```sql
--注意两台必须全部执行
create database msb;
```

### 4、在主（node1）服务器进行如下配置：

```shell
#修改配置文件，执行以下命令打开mysql配置文件
vi /etc/my.cnf
#在mysqld模块中添加如下配置信息
log-bin=master-bin #二进制文件名称
binlog-format=ROW  #二进制日志格式，有row、statement、mixed三种格式，row指的是把改变的内容复制过去，而不是把命令在从服务器上执行一遍，statement指的是在主服务器上执行的SQL语句，在从服务器上执行同样的语句。MySQL默认采用基于语句的复制，效率比较高。mixed指的是默认采用基于语句的复制，一旦发现基于语句的无法精确的复制时，就会采用基于行的复制。
server-id=1		   #要求各个服务器的id必须不一样
binlog-do-db=msb   #同步的数据库名称
```

### 5、配置从服务器登录主服务器的账号授权

```sql
--授权操作
set global validate_password_policy=0;
set global validate_password_length=1;
grant replication slave on *.* to 'root'@'%' identified by '123456';
--刷新权限
flush privileges;
```

### 6、从服务器的配置

```shell
#修改配置文件，执行以下命令打开mysql配置文件
vi /etc/my.cnf
#在mysqld模块中添加如下配置信息
log-bin=master-bin	#二进制文件的名称
binlog-format=ROW	#二进制文件的格式
server-id=2			#服务器的id
```

### 7、重启主服务器的mysqld服务

```shell
#重启mysql服务
service mysqld restart
#登录mysql数据库
mysql -uroot -p
#查看master的状态
show master status；
```

![1570703264912](..\image\typora-user-images\1570703264912.png)

### 8、重启从服务器并进行相关配置

```shell
#重启mysql服务
service mysqld restart
#登录mysql
mysql -uroot -p
#连接主服务器
change master to master_host='192.168.85.11',master_user='root',master_password='123456',master_port=3306,master_log_file='master-bin.000001',master_log_pos=154;
#启动slave
start slave
#查看slave的状态
show slave status\G(注意没有分号)
```

### 9、此时可以在主服务器进行相关的数据添加删除工作，在从服务器看相关的状态



`如果大黄蜂播放器网校不是平凡之路 大飞机课堂，说明是翻录的，举报翻录添加微信：meetjava，举报者免费送课`

`如果大黄蜂播放器网校不是平凡之路 大飞机课堂，说明是翻录的，举报翻录添加微信：meetjava，举报者免费送课`

`如果大黄蜂播放器网校不是平凡之路 大飞机课堂，说明是翻录的，举报翻录添加微信：meetjava，举报者免费送课`



##### `另外，`回收课程

​	`回收一些正版课程，买过任何正版课程的可以联系，支持换课，换VIP，或代下载`
​	`回收一些正版课程，买过任何正版课程的可以联系，支持换课，换VIP，或代下载`
​	`回收一些正版课程，买过任何正版课程的可以联系，支持换课，换VIP，或代下载`



##### **收藏下面学习目录链接，学课永不迷路**，点击获取全网it目录

https://www.yuque.com/javadd/itcourse/list



**微信：meetjava，认准一手，学习无忧，全网it课程都有**

**微信：meetjava，认准一手，学习无忧，全网it课程都有**



##### 重磅，**收代理 冲冲冲**

