# [【Mybatis】TKMybatis 介绍和使用](https://blog.csdn.net/qq_34416331/article/details/106322596)


目录

一、什么是 TKMybatis

二、TKMybatis 使用

2.1 Springboot 项目中加入依赖

2.2 使用讲解

2.2.1 实体类中使用

2.2.2 dao中使用

2.2.3 Service 层中使用

2.3 实际案例

2.3.1 dao 层使用

2.3.2 service 层使用

一、什么是 TKMybatis
TKMybatis 是基于 Mybatis 框架开发的一个工具，内部实现了对单表的基本数据操作，只需要简单继承 TKMybatis 提供的接口，就能够实现无需编写任何 sql 即能完成单表操作。

 

二、TKMybatis 使用
2.1 Springboot 项目中加入依赖

```java
<!--通用mapper起步依赖-->
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.0.4</version>
</dependency>
```

在 POJO 类中加入依赖

```java
<dependency>
    <groupId>javax.persistence</groupId>
    <artifactId>persistence-api</artifactId>
    <version>1.0</version>
    <scope>compile</scope>
</dependency>

```

在启动类中配置 @MapperScan 扫描

```java
@SpringBootApplication
@MapperScan(basePackages = {"com.tom.order.mapper"})
public class OrderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
}
```




2.2 使用讲解
2.2.1 实体类中使用
在实体类中，常用的注解和意义为：

@Table：描述数据库表信息，主要属性有name(表名)、schema、catalog、uniqueConstraints等。

@Id：指定表主键字段，无属性值。

@Column：描述数据库字段信息，主要属性有name(字段名)、columnDefinition、insertable、length、nullable(是否可为空)、precision、scale、table、unique、updatable等。

@ColumnType：描述数据库字段类型，可对一些特殊类型作配置，进行特殊处理，主要属性有jdbcType、column、typeHandler等。

其他注解如：@Transient、@ColumnResult、@JoinColumn、@OrderBy、@Embeddable等暂不描述

 

2.2.2 dao中使用
单表操作，只需要继承 tk.mybatis 下的 Mapper 接口即可使用

import tk.mybatis.mapper.common.Mapper;

@Repository
public interface BrandMapper extends Mapper<Brand> {
}
查看具体使用：内部都已经封装了基本的单表操作



 

2.2.3 Service 层中使用
操作	类型	介绍
增加	Mapper.insert(record)；	保存一个实体，null的属性也会保存，不会使用数据库默认值
Mapper.insertSelective(record)；	保存一个实体，忽略空值，即没提交的值会使用使用数据库默认值

删除	Mapper.delete(record);	根据实体属性作为条件进行删除，查询条件使用等号
Mapper.deleteByExample(example)	根据Example条件删除数据
Mapper.deleteByPrimaryKey(key)	根据主键字段进行删除，方法参数必须包含完整的主键属性

修改	Mapper.updateByExample(record,example)	根据Example条件更新实体`record`包含的全部属性，null值会被更新
Mapper.updateByExampleSelective(record, example)	根据Example条件更新实体`record`包含的不是null的属性值
Mapper.updateByPrimaryKey(record)	根据主键更新实体全部字段，null值会被更新
Mapper.updateByPrimaryKeySelective(record)	根据主键更新属性不为null的值

查询	Mapper.select(record)	根据实体中的属性值进行查询，查询条件使用等号
Mapper.selectAll()	查询全部结果
Mapper.selectByExample(example)	根据Example条件进行查询
Mapper.selectByPrimaryKey(key)	根据主键字段进行查询，方法参数必须包含完整的主键属性，查询条件使用等号
Mapper.selectCount(record)	根据实体中的属性查询总数，查询条件使用等号
Mapper.selectCountByExample(example)	根据Example条件进行查询总数
Mapper.selectOne(record)	
根据实体中的属性进行查询，只能有一个返回值，有多个结果是抛出异常，查询条件使用等号。

但是如果存在某个属性为int，则会初始化为0。可能影响到实际使用

 

2.3 实际案例
2.3.1 dao 层使用

import tk.mybatis.mapper.common.Mapper;

/**
 * DAO 使用通用Mapper
 * DSO接口需要继承 tk.mybatis.mapper.common.Mapper
 */
 @Repository
 public interface BrandMapper extends Mapper<Brand> {


}


2.3.2 service 层使用
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import org.apache.commons.lang.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import tk.mybatis.mapper.entity.Example;

import java.util.List;

@Service
public class BrandServiceImpl implements BrandService {

    @Autowired
    private BrandMapper brandMapper;
     
    public Example createExample(Brand brand) {
        // 自定义条件搜索对象 Example
        Example example = new Example(Brand.class);
        Example.Criteria criteria = example.createCriteria(); //条件构造器
     
        if (brand != null) {
            if (!StringUtils.isEmpty(brand.getName())) {
                criteria.andLike("name", '%' + brand.getName() + '%');
            }
     
            if (!StringUtils.isEmpty(brand.getLetter())) {
                criteria.andEqualTo("letter", brand.getLetter());
            }
        }
        return example;
    }
     
    @Override
    public List<Brand> findAll() {
        return brandMapper.selectAll();
    }
     
    @Override
    public List<Brand> findList(Brand brand) {
        Example example = createExample(brand);
        return brandMapper.selectByExample(example);
    }
     
    @Override
    public Brand findById(Integer id) {
        return brandMapper.selectByPrimaryKey(id);
    }
     
    /**
     * 分页查询
     * @param page  当前页
     * @param size  每页显示的条数
     * @return
     */
    @Override
    public PageInfo<Brand> pageSearch(Integer page, Integer size) {
        // 分页实现
        // 后面的查询必须是紧跟集合查询
        PageHelper.startPage(page, size);
        // 查询集合
        List<Brand> brands = brandMapper.selectAll();
        return new PageInfo<Brand>(brands);
    }
     
    @Override
    public PageInfo<Brand> pageSearchAndCondition(Brand brand, Integer page, Integer size) {
        // 开始分页
        PageHelper.startPage(page, size);
        // 搜索数据
        Example example = createExample(brand);
        List<Brand> list = brandMapper.selectByExample(example);
        return new PageInfo<Brand>(list);
    }
     
    /**
     * 增加品牌
     * @param brand
     */
    @Override
    public void add(Brand brand) {
        // 使用通用 Mapper.insertSelective
        // 方法中但凡带有selective就会忽略空值
        int i = brandMapper.insertSelective(brand);
    }
     
    /**
     * 根据id修改品牌
     * @param brand
     */
    @Override
    public void update(Brand brand) {
        // 使用通用mapper.update();
        brandMapper.updateByPrimaryKeySelective(brand);
    }
     
    /**
     * 根据id删除
     * @param id
     */
    @Override
    public void del(Integer id) {
        brandMapper.deleteByPrimaryKey(id);
    }
}

三、扩展介绍
https://juejin.im/post/5cbfd158f265da038860b82f