# [通用 Mapper @KeySql 注解 genId 方法详解](https://dandelioncloud.cn/article/details/1529633692721102850/)

 2022-05-26 09:21 42阅读 [0赞](https://dandelioncloud.cn/article/details/1529633692721102850#comments)



为了方便使用全局主键（例如：Vesta 是一款通用的ID产生器，互联网俗称统一发号器），通用 Mapper 4.0.2 版本增加了新的控制主键生成的策略。

`@KeySql` 注解增加了下面的方法：

```
/** * Java 方式生成主键，可以和发号器一类的服务配合使用 * * @return */Class<? extends GenId> genId() default GenId.NULL.class;
```

使用该功能的时候，需要配置 `genId` 属性。
由于生成主键的方式通常和使用环境有关，因此通用 Mapper 没有提供默认的实现。

`GenId` 接口如下：

```
public interface GenId<T> {     class NULL implements GenId {        @Override        public Object genId(String table, String column) {            throw new UnsupportedOperationException();        }    }    T genId(String table, String column);}
```

通过接口方法可以看出，在生成 Id 时，可以得到当前的表名和列名，我们可以使用这两个信息生成和表字段相关的 Id 信息。也可以完全不使用这两个信息，生成全局的 Id。

**使用 genId 方式时，字段的值可以回写！**

## 示例一，使用 UUID

> 完整测试代码：
> https://github.com/abel533/Mapper/tree/master/base/src/test/java/tk/mybatis/mapper/base/genid

### 1. 实现 `GenId`

使用 UUID 方式时，首先我们需要提供一个能产生 UUID 的实现类：

```
public class UUIdGenId implements GenId<String> {     @Override    public String genId(String table, String column) {        return UUID.randomUUID().toString();    }}
```

这个实现类就是简单的返回了一个 `String` 类型的值，并且没有处理 UUID 中的 `-`。

### 2. 配置 `genId`

例如有如下表(hsqldb 数据库)：

```
create table user ( id varchar(64) NOT NULL PRIMARY KEY, name varchar(32), code VARCHAR(2) );
```

对应如下实体：

```
public class User {     @Id    @KeySql(genId = UUIdGenId.class)    private String id;    private String name;    private String code;    public User() {    }    public User(String name, String code) {        this.name = name;        this.code = code;    }    //省略 setter 和 getter}
```

我们只需要在注解中配置 `@KeySql(genId = UUIdGenId.class)` 即可，需要注意的是，如果你使用了 `@KeySql` 提供的其他方式，`genId` 就不会生效，`genId` 是所有方式中优先级最低的。

### 3. 测试

```
@Testpublic void testUUID(){    SqlSession sqlSession = getSqlSession();    try {        UserMapper mapper = sqlSession.getMapper(UserMapper.class);        for (int i = 0; i < countries.length; i++) {            User user = new User(countries[i][0], countries[i][1]);            Assert.assertEquals(1, mapper.insert(user));            Assert.assertNotNull(user.getId());            System.out.println(user.getId());        }    } finally {        sqlSession.close();    }}
```

输出的部分日志如下：

```
DEBUG [main] - Setting autocommit to false on JDBC Connection [org.hsqldb.jdbc.JDBCConnection@3eb7fc54]DEBUG [main] - ==>  Preparing: INSERT INTO user ( id,name,code ) VALUES( ?,?,? ) DEBUG [main] - ==> Parameters: 94ee80ac-d156-412d-b63e-b75b49026874(String), Angola(String), AO(String) DEBUG [main] - <== Updates: 1 94ee80ac-d156-412d-b63e-b75b49026874 DEBUG [main] - ==> Preparing: INSERT INTO user ( id,name,code ) VALUES( ?,?,? ) DEBUG [main] - ==> Parameters: 0f21aab9-0637-4b7f-ade9-fe5bc4cf1693(String), Afghanistan(String), AF(String) DEBUG [main] - <== Updates: 1 0f21aab9-0637-4b7f-ade9-fe5bc4cf1693
```

## 示例二，简单的全局时序ID

> 完整测试代码：
> https://github.com/abel533/Mapper/tree/master/base/src/test/java/tk/mybatis/mapper/base/genid

### 1. 实现 `GenId`

使用 UUID 方式时，首先我们需要提供一个能产生 UUID 的实现类：

```
public class SimpleGenId implements GenId<Long> {     private Long    time;    private Integer seq;    @Override    public synchronized Long genId(String table, String column) {        long current = System.currentTimeMillis();        if (time == null || time != current) {            time = current;            seq = 1;        } else if (current == time) {            seq++;        }        return (time << 20) | seq;    }}
```

这是一个简单的实现，通过同步方法保证唯一，**不考虑任何特殊情况，不要用于生产环境。**

### 2. 配置 `genId`

例如有如下表(hsqldb 数据库)：

```
create table country ( id bigint NOT NULL PRIMARY KEY, countryname varchar(32), countrycode VARCHAR(2) );
```

对应如下实体：

```
public class Country {     @Id    @KeySql(genId = SimpleGenId.class)    private Long id;    private String countryname;    private String countrycode;    public Country() {    }    public Country(String countryname, String countrycode) {        this.countryname = countryname;        this.countrycode = countrycode;    }    //省略 setter 和 getter}
```

我们只需要在注解中配置 `@KeySql(genId = SimpleGenId.class)` 即可，需要注意的是，如果你使用了 `@KeySql` 提供的其他方式，`genId` 就不会生效，`genId` 是所有方式中优先级最低的。

### 3. 测试

```
@Testpublic void testGenId(){    SqlSession sqlSession = getSqlSession();    try {        CountryMapper mapper = sqlSession.getMapper(CountryMapper.class);        for (int i = 0; i < countries.length; i++) {            Country country = new Country(countries[i][0], countries[i][1]);            Assert.assertEquals(1, mapper.insert(country));            Assert.assertNotNull(country.getId());            System.out.println(country.getId());        }    } finally {        sqlSession.close();    }}
```

输出的部分日志如下：

```
DEBUG [main] - Setting autocommit to false on JDBC Connection [org.hsqldb.jdbc.JDBCConnection@96def03]DEBUG [main] - ==>  Preparing: INSERT INTO country ( id,countryname,countrycode ) VALUES( ?,?,? ) DEBUG [main] - ==> Parameters: 1598437075106922497(Long), Angola(String), AO(String) DEBUG [main] - <== Updates: 1 1598437075106922497 DEBUG [main] - ==> Preparing: INSERT INTO country ( id,countryname,countrycode ) VALUES( ?,?,? ) DEBUG [main] - ==> Parameters: 1598437075176128513(Long), Afghanistan(String), AF(String) DEBUG [main] - <== Updates: 1 1598437075176128513 DEBUG [main] - ==> Preparing: INSERT INTO country ( id,countryname,countrycode ) VALUES( ?,?,? ) DEBUG [main] - ==> Parameters: 1598437075178225665(Long), Albania(String), AL(String) DEBUG [main] - <== Updates: 1 1598437075178225665
```

## 示例三，基于 Vesta 的实现

通过前面两个例子应该能看出，通过不同的 `GenId` 实现就能切换不同的 ID 生成方式，ID 的类型需要保证一致。

这里提供一个能应用于生产环境的思路，由于和具体环境有关，因此这里没有直接可用的代码。

> Vesta 地址：https://gitee.com/robertleepeak/vesta-id-generator

示例代码：

```
public class VestaGenId implement GenId<Long> {    public Long genId(String table, String column){       //ApplicationUtil.getBean 需要自己实现       IdService idService = ApplicationUtil.getBean(IdService.class);       return idService.genId();   }}
```

这个代码也很简单，由于注解只能配置类，不能注入，因此 `VestaGenId` 实现中不能直接注入 `IdService`，需要通过静态方法从 Spring Context 中获取，获取后就可以正常使用了。

> 对 Spring 熟悉的人可以很容易理解 `ApplicationUtil.getBean` 方法。
> 如果对 Spring 不了解，可以看看 http://sb33060418.iteye.com/blog/2372874 这里的**SpringContextHolder**。

## 特殊的地方

使用 `genId` 时，在和数据库交互前，ID 值就已经生成了，由于这个 ID 和数据库的机制无关，因此一个实体中可以出现任意个 使用 `genId` 方式的 `@KeySql` 注解，这些注解可以指定不同的实现方法。

## 还没完，InsertListMapper 特殊支持

通用 Mapper 提供了好几个 `InsertListMapper` 接口，最早提供的都是和数据库相关的方法，所以在 Id 策略上都有和数据库相关的限制。

最新增加的 `tk.mybatis.mapper.additional.insert.InsertListMapper` 接口是一个和数据库无关的方法，他不支持任何的主键策略。但是有了 `genId` 方式后，这个接口增加了对 `@KeySql` 注解 `genId` 方法的支持。

下面是一个示例。

> 测试地址：
> https://github.com/abel533/Mapper/tree/master/extra/src/test/java/tk/mybatis/mapper/additional/insertlist

使用前面 **示例一** 中的 `User` 实体和表。

接口定义：

```
import tk.mybatis.mapper.additional.insert.InsertListMapper;public interface UserMapper extends InsertListMapper<User> { }
```

测试代码：

```
@Testpublic void testInsertList() {    SqlSession sqlSession = getSqlSession();    try {        UserMapper mapper = sqlSession.getMapper(UserMapper.class);        List<User> userList = new ArrayList<User>(countries.length);        for (int i = 0; i < countries.length; i++) {            userList.add(new User(countries[i][0], countries[i][1]));        }        Assert.assertEquals(countries.length, mapper.insertList(userList));        for (User user : userList) {            Assert.assertNotNull(user.getId());            System.out.println(user.getId());        }    } finally {        sqlSession.close();    }}
```

部分日志：

```
DEBUG [main] - Setting autocommit to false on JDBC Connection [org.hsqldb.jdbc.JDBCConnection@778d1062]DEBUG [main] - ==>  Preparing: INSERT INTO user ( id,name,role ) VALUES ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) , ( ?,?,? ) DEBUG [main] - ==> Parameters: 1b8511b7-304a-47a7-abec-c2f38e498e69(String), Angola(String), AO(String), 2d31db7e-5d4c-4997-9db3-44163018a3c7(String), Afghanistan(String), AF(String), 31ac04d7-cb61-45b2-9b7d-d75e82a65529(String), Albania(String), AL(String), 39d77534-bf9a-47d4-aaa0-63d32c2d4562(String), Algeria(String), DZ(String), 5186f2e5-57b1-4837-8e06-0392f70e6dbe(String), Andorra(String), AD(String), 4ccab2fb-db59-4588-ae90-c33e9acc5a84(String), Anguilla(String), AI(String),
```

通过 `genId` 方式可以批量插入，并且可以回写 ID。