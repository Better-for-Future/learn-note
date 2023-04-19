# [java 中枚举的定义](https://blog.csdn.net/guliudeng/article/details/127135875)


枚举的意义
在开发过程中，我们会经常遇到一些属性或者参数定义为某个固定的值，我们通常会将这些值定义为常量或者枚举，不用我们再一一定义一遍了，只需要在需要使用的地方引入定义好的常量或者枚举即可，也方便我们日后再维护代码的时候，只需要改一个地方，其他的地方也都随只改变。

核心思想：
先创建一个枚举类

定义你需要的枚举对象的成员属性，例如：name，age之类的属性

然后提供属性相对应的get和set方法，以及对象的有参构造器

定义你想要的对象枚举值，这样一个最基本的枚举类就被定义好了

也可以自定义拓展一下方法，比如根据姓名获取枚举中对应年龄的方法，根据年龄获取枚举中与年龄对应的姓名等，下面是一个简单的案例

```java
public enum RspEnum {
    SUCCESS("响应成功",1000),
    FAILED("响应失败",8888);
    private String desc;
    private int code;
  RspEnum(String desc, int code) {
      this.desc = desc;
      this.code = code;
  }
  //根据响应码获取响应描述
  public static String getName(int code) {
      for (RspEnum value : RspEnum.values()) {
          if (value.getCode()  == code) {
              return value.getDesc();
          }
      }
      return null;
  }
  //根据响应描述来返回响应码
  public static Integer getCode(String desc) {
      for (RspEnum value : RspEnum.values()) {
          if (value.getDesc().equals(desc)) {
              return value.getCode();
          }
      }
      return null;
  }
 
  public String getDesc() {
      return desc;
  }
 
  public void setDesc(String desc) {
      this.desc = desc;
  }
 
  public int getCode() {
      return code;
  }
 
  public void setCode(int code) {
      this.code = code;
  }
}
```





    


补充：
        类中RspEnum.values()方法是枚举本身的方法，调用枚举的values()方法可以获取到枚举的集合，然后对次遍历操作。int类型定义“0000”输出的是“0”这块要注意一下。
