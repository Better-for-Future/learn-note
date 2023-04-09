# @Transient

![img](https://csdnimg.cn/release/blogv2/dist/pc/img/reprint.png)

[雪心玉竹](https://blog.csdn.net/rongxiang111)![img](https://csdnimg.cn/release/blogv2/dist/pc/img/newCurrentTime2.png)于 2019-01-14 13:37:55 发布![img](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes2.png)110202![img](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect2.png) 收藏 147

分类专栏： [Java](https://blog.csdn.net/rongxiang111/category_6392625.html) 文章标签： [transient](https://so.csdn.net/so/search/s.do?q=transient&t=all&o=vip&s=&l=&f=&viparticle=)

[![img](https://img-blog.csdnimg.cn/20190918140158853.png?x-oss-process=image/resize,m_fixed,h_224,w_224)Java专栏收录该内容](https://blog.csdn.net/rongxiang111/category_6392625.html)

20 篇文章2 订阅

订阅专栏

  java 的transient关键字的作用是需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。

### 用法

@transient 就是在给某个javabean上需要添加个属性，但是这个属性你又不希望给存到数据库中去，仅仅是做个临时变量，用一下。不修改已经存在数据库的数据的数据结构。

那么这个注解就可以一用。

只要在你准备添加的临时属性上添加这个注解，然后getter和setter自动完成一下，就可以啦。

```java
@Document(collection = "streams")
public class Stream implements Serializable,Cloneable {
    private static final long serialVersionUID = 1L;
    @Transient
    private boolean alarmsettingsActive = false;
xxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxx
}
12345678
```

上面的是个人理解，下面呢就是详细说说。

### transient的作用

  我们都知道一个对象只要实现了Serilizable接口，这个对象就可以被序列化，java的这种序列化模式为开发者提供了很多便利，我们可以不必关系具体序列化的过程，只要这个类实现了Serilizable接口，这个类的所有属性和方法都会自动序列化。
  然而在实际开发过程中，我们常常会遇到这样的问题，这个类的有些属性需要序列化，而其他属性不需要被序列化，打个比方，如果一个用户有一些敏感信息（如密码，银行卡号等），为了安全起见，不希望在网络操作（主要涉及到序列化操作，本地序列化缓存也适用）中被传输，这些信息对应的变量就可以加上transient关键字。换句话说，这个字段的生命周期仅存于调用者的内存中而不会写到磁盘里持久化。
  总之，java 的transient关键字为我们提供了便利，你只需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。

### transient使用小结

1）一旦变量被transient修饰，变量将不再是对象持久化的一部分，该变量内容在序列化后无法获得访问。

2）transient关键字只能修饰变量，而不能修饰方法和类。注意，本地变量是不能被transient关键字修饰的。变量如果是用户自定义类变量，则该类需要实现Serializable接口。

3）被transient关键字修饰的变量不再能被序列化，一个静态变量不管是否被transient修饰，均不能被序列化。