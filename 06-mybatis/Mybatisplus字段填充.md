https://blog.csdn.net/weixin_46295656/article/details/114548990



- 自定义实现类 MyMetaObjectHandler ， 我们就把他放在handler目录下。这个类就是实现MetaObjectHandler 类，重写两个方法，一个当mp实现添加操作的时候，这个方法执行，一个当mp实现修改操作的时候，这个方法执行。还有别忘记
  添加@Component注解，交给spring管理。
  