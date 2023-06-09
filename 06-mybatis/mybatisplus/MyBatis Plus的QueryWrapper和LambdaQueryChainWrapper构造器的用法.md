# [MyBatis Plus的QueryWrapper和LambdaQueryChainWrapper构造器的用法](https://blog.csdn.net/WYP123456L/article/details/122544143)

WYP@LML

于 2022-01-17 17:16:06 发布

4488
 收藏 6
文章标签： spring boot 数据结构 数据库
版权

华为云开发者联盟
该内容已被华为云开发者联盟社区收录
加入社区
一.QueryWrapper

        1、QueryWrapper是什么
queryWrapper是mybatis plus中实现查询的对象封装操作类，他的层级关系如下



 

在上面的图片中，

Wrapper ： 条件构造抽象类，最顶端父类，抽象类中提供4个方法西面贴源码展示
AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件
AbstractLambdaWrapper ： Lambda 语法使用 Wrapper统一处理解析 lambda 获取 column。
LambdaQueryWrapper ：看名称也能明白就是用于Lambda语法使用的查询Wrapper
LambdaUpdateWrapper ： Lambda 更新封装Wrapper
QueryWrapper ： Entity 对象封装操作类，不是用lambda语法
UpdateWrapper ： Update 条件封装，用于Entity对象更新操作

        2、使用demo
使用的关键的代码
QueryWrapper<PbListBlack> sectionQueryWrapper = new QueryWrapper<>();
sectionQueryWrapper.eq("OPTYPE", 1);
sectionQueryWrapper.eq("BLTYPE", 1);
List<PbListBlack> pbListBlacks = iPbListBlackMapper.selectList(sectionQueryWrapper);

上面这段代码的意思就是，首先新建一个QueryWrapper对象，类型为PbListBlack对象，也就是你需要查询的实体数据，

sectionQueryWrapper.eq("OPTYPE", 1);

sectionQueryWrapper.eq("BLTYPE", 1);

这两句的意思是PbListBlack对象对应的数据库表中的OPTYPE，BLTYPE字段值要为1

然后调用iPbListBlackMapper.selectList方法，入参就为前面新建好的查询对象封装类

下面贴上查询实体的代码

@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("PB_LIST_BLACK")
@ApiModel(value = "PB_LIST_BLACK对象", description = "")
public class PbListBlack extends Model<PbListBlack> {
   @TableField("ID")
   private Long id;

   @TableField("USERID")
   private String userId;
   @TableField("SPGATE")
   private String spgate;
   @TableField("SPNUMBER")
   private String spnumber;
   @TableField("PHONE")
   private Long phone;
   @TableField("OPTYPE")
   private Integer optype;
   @TableField("OPTTIME")
   private Timestamp optTime;
   @TableField("CORPCODE")
   private String corpCode;
   @TableField("SVRTYPE")
   private String svrType;

   @TableField("MSG")
   private String msg = " ";

   @ApiModelProperty(value = "黑名单类型。1：短信；2：彩信")
   @TableField("BLTYPE")
   private Integer blType;
}
然后是iPbListBlackMapper的代码‘，代码很简单

@Repository
public interface IPbListBlackMapper extends BaseMapper<PbListBlack> {
}

仅仅只需要基层BaseMapper接口即可，这样mybatis plus底层封装的方法即可实现帮你查询你设置查询条件查询到的数据

当你的Mapper 继承BaseMapper接口后，无需编写 mapper.xml 文件，即可获得CRUD功能

              3.queryWrapper的方法


    /**
      * 条件构造器 查询操作
      */
     @Test
    void TestQueryWrapperSelect() {
        //1、条件用法
        List<User> userList = userMapper.selectList(new QueryWrapper<User>()
                 .like("email", "24252")
                 .between("age", 20, 22)
                 .or()
                 .eq("name", "zcx")
         );
         System.out.println("userList:" + userList);
     
         //2、排序用法
        List<User> users = userMapper.selectList(new QueryWrapper<User>()
                 .eq("nick_name", "xx")
                 .orderByAsc("age")  //升序
                 .orderByDesc("age") //降序
                 .last("limit 0,3") //last用法：在sql末尾添加sql语句，有sql注入风险
         );
         System.out.println("users:"+users);
     
    }
二.LambdaQueryChainWrapper

//链式查询方式
User one = new LambdaQueryChainWrapper<>(userMapper)
                 .eq(User::getName, "liangd1")
                 .one();
 @Test
      void TestLambdaQueryChainWrapper() {
        //1、eq查询单条
         User one = new LambdaQueryChainWrapper<>(userMapper)
                .eq(User::getName, "liangd1")
                .one();
         System.out.println("UserOne:" + one);

          //2、查询list
        List<User> users = new LambdaQueryChainWrapper<>(userMapper)
                .eq(User::getName, "zcx")
                 .list();
         System.out.println("UserList:" + users);
     
        //3、模糊查询
         List<User> LikeList = new LambdaQueryChainWrapper<>(userMapper)
                 .like(User::getEmail, "test")
                .list();
        System.out.println("LikeUser:" + LikeList);
    }

————————————————
版权声明：本文为CSDN博主「WYP@LML」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/WYP123456L/article/details/122544143