bad SQL grammer []； nested exception is java.sql.SQLSyntaxErrorException:ORA-00918:未明确定义列

善守的大龙猫

于 2020-09-09 20:56:38 发布

1839
 收藏 1
分类专栏： 工作记录 文章标签： SQL
版权

工作记录
专栏收录该内容
46 篇文章2 订阅
订阅专栏
**出现这个问题主要是因为列名重复，导致不能明确定义列。**

很多表及字段关联以后，往往会出现这个问题，还是要细心才能解决。

————————————————
版权声明：本文为CSDN博主「善守的大龙猫」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_43221207/article/details/108502163