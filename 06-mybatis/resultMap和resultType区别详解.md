# [resultMap和resultType区别详解](https://www.cnblogs.com/yeyuting/p/14182741.html)



resultmap与resulttype的区别为：对象不同、描述不同、类型适用不同

一、对象不同

1、resultmap：resultMap如果查询出来的列名和pojo的属性名不一致，通过定义一个resultMap对列名和pojo属性名之间作一个映射关系。

2、resultType：resultType使用resultType进行输出映射，只有查询出来的列名和pojo中的属性名一致，该列才可以映射成功。

二、描述不同

1、resultmap：resultMap对于一对一表连接的处理方式通常为在主表的pojo中添加嵌套另一个表的pojo，然后在mapper.xml中采用association节点元素进行对另一个表的连接处理。

2、resulTtype：resultType无法查询结果映射到pojo对象的pojo属性中，根据对结构集查询遍历的需要选择使用resultType还是resultMap。适用于单表查询。

三、类型适用不同

1、resultmap：mybatis中在查询进行select映射的时候，返回类型可以用resultType，也可以用resultMap。

2、resulttype：resultType是直接表示返回类型的,而resultMap则是对外部ResultMap的引用，但是resultType跟resultMap不能同时存在。

**resultMap示例：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<resultMap id="userRoleListMapSelect" extends="userMap"
               type="com.example.simple.model.SysUser">
        <collection property="roleList"
                    fetchType="lazy"
                    column="{userId = id}"
                    select="com.example.simple.mapper.RoleMapper.selectRoleByUserId"/>
    </resultMap>
    <select id="selectAllUserAndRolesSelect" resultMap="userRoleListMapSelect">
        select
        u.id,
        u.user_name,
        u.user_password,
        u.user_email ,
        u.user_info ,
        u.head_img,
        u.create_time
        from sys_user u
        where u.id = #{id}

    </select>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**resultType示例：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<select id="selectRolesByUserAndRole"
            resultType="com.example.simple.model.SysRole">
        SELECT r.id,
                r.role_name,
                r.enabled,
                r.create_by,
                r.create_time
                FROM mybatis.sys_user u
                inner join mybatis.sys_user_role ur on u.id = ur.user_id
                INNER JOIN mybatis.sys_role r on ur.role_id = r.id
                where u.id = #{user.id}  and r.enabled = #{role.enabled}

    </select>
```