# [注解 @RequiredArgsConstructor](https://blog.csdn.net/qq_37256896/article/details/115869717)

生成带有必需参数的构造函数。 必需的参数是最终字段和具有约束的字段，例如@NonNull 。
完整的文档可在@lconstructor的项目lombok功能页面上找到 。即使未列出，该注释也具有onConstructor参数。 有关更多详细信息，请参见完整的文档。

这个是基于lombok的使用时必须导入lombok包。

```java
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
</dependency>
```


同时注意几点：
```html
1.必须声明的变量为final

2.根据构造器注入的，相当于当容器调用带有一组参数的类构造函数时，基于构造函数的 DI 就完成了，其中每个参数代表一个对其他类的依赖。基于构造方法为属性赋值，容器通过调用类的构造方法将其进行依赖注入使用当我们需要注入Bean的时候可以直接在类名称上使用。代替了Autowrited注解

```



```java
@RequiredArgsConstructor
@RestController
@RequestMapping("/omponent")
public class ComponentController {
    private final ComponentService ComponentService;
}    

```





# [@RequiredArgsConstructor的使用](https://blog.csdn.net/qq_38457494/article/details/124593190?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-124593190-blog-115869717.235%5Ev30%5Epc_relevant_yljh&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-124593190-blog-115869717.235%5Ev30%5Epc_relevant_yljh&utm_relevant_index=1)


这个也是一个构造器注入的方法之一

使用RequiredArgsConstructor 注入的和Service和Autowired有区别：

```html
1.写在类上

2.要注入的参数要添加一个final修改。
```

```js
注意　　final　不能去掉， 如果去掉就会导致注入不进去。

@RequiredArgsConstructor主要是对当前类中带有final 的属性进行属性注入。
```

