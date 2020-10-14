

# MyBatis

## 1.mybatis中$和#的区别

1. `＃{}`将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。如：order by #{id}，如果传入的值是111,那么解析成sql时的值为order by “111”, 如果传入的值是id，则解析成的sql为order by “id”。
2. `${}`将传入的数据直接显示生成在sql中。如：order by 
   ${id}，如果传入的值是111,那么解析成sql时的值为order by 111, 如果传入的值是id，则解析成的sql为order 
   by id。
3. `#`方式能够很大程度防止sql注入。
4. `$`方式无法防止Sql注入。
5. `$`方式一般用于传入数据库对象，例如传入表名.
6. 一般能用#的就别用$

## 2.什么是sql注入？

#### 1.什么是SQL注入？

SQL注入攻击，简称SQL攻击或注入攻击，是发生于应用程序之数据库层的安全漏洞。简而言之，是在输入的字符串之中注入SQL指令，在设计不良的程序当中忽略了检查，那么这些注入进去的指令就会被数据库服务器误认为是正常的SQL指令而运行，因此遭到破坏或是入侵。

最常见的就是我们在应用程序中使用字符串联结方式组合 SQL 指令，有心之人就会写一些特殊的符号，恶意篡改原本的 SQL 语法的作用，达到注入攻击的目的。

举个栗子：

比如验证用户登录需要 username 和 password，编写的 SQL 语句如下：

```mysql
select * from user where (name = '"+ username +"') and (pw = '"+ password +"');
```

username 和 password 字段被恶意填入

```mysql
username = "1' OR '1'='1";
```

```mysql
password = "1' OR '1'='1";
```

将导致原本的 SQL 字符串被填为：

```mysql
select * from user where (name = '1' or '1'='1') and (pw = '1' or '1'='1');
```

实际上运行的 SQL 语句将变成：

```mysql
select * from user;
```

也就是不再需要 username 和 password 账密即达到登录的目的，结果不言而喻。

#### 2.mybatis解决SQL注入问题

我们使用 mybatis 编写 SQL 语句时，难免会使用模糊查询的方法，mybatis 提供了两种方式 `#{}` 和 `${}` 。

- `#{value}` 在预处理时，会把参数部分用一个占位符 ? 替代，其中 value 表示接受输入参数的名称。能有效解决 SQL 注入问题
- `${}` 表示使用拼接字符串，将接受到参数的内容不加任何修饰符拼接在 SQL 中，使用`${}`拼接 sql，将引起 SQL 注入问题。

举个例子：

1 查询数据库 sample 表 user 中的记录，我们故意使用特殊符号，看能否引起 SQL 注入。使用 mybatis 在 mapper.xml 配置文件中编写 SQL 语句，我们先采用拼接字符串形式，看看结果如何：

```mysql
<select id="findUserByName" parameterType="java.lang.String" resultType="cn.itcast.mybatis.po.User">
        <!-- 拼接 MySQL,引起 SQL 注入 -->
        SELECT * FROM user WHERE username LIKE '%${value}%'
    </select>
```

注意在配置文件中编写 SQL 语句时，后边不需要加分号。

调用配置文件，编写测试文件，查询数据库内容，采用特殊符号，引起 SQL 注入：

```java
@Test
    public void testFindUserByName() throws Exception{

        SqlSession sqlSession=sqlSessionFactory.openSession();

        //创建UserMapper代理对象
        UserMapper userMapper=sqlSession.getMapper(UserMapper.class);

        //调用userMapper的方法
        List<User> list=userMapper.findUserByName("' or '1'='1");

        sqlSession.close();

        System.out.println(list);
    }
}
```

![image-20201009233836337](/Users/hexin/Library/Application Support/typora-user-images/image-20201009233836337.png)



可以看到执行语句其实变为了

```mysql
select * from user
```

将user 表中的全部记录打印出来了。发生了 SQL 注入。

2 如果将配置文件中的 SQL 语句改成 `#{}` 形式，可避免 SQL 注入。

```mysql
<select id="findUserByName" parameterType="java.lang.String" resultType="cn.itcast.mybatis.po.User">
        <!-- 使用 SQL concat 语句,拼接字符串,防止 SQL 注入 -->
        SELECT * FROM USER WHERE username LIKE CONCAT('%',#{value},'%' )
    </select>
```

![image-20201009233942774](/Users/hexin/Library/Application Support/typora-user-images/image-20201009233942774.png)

可以看到程序中参数部分用 ? 替代了，很好地解决了 SQL 语句的问题，防止了 SQL 注入。查询结果将为空。

## 3.Mybatis批量更新的方法

方式一:

```mysql
<update id="updateBatch"  parameterType="java.util.List">  
    <foreach collection="list" item="item" index="index" open="" close="" separator=";">
        update tableName
        <set>
            name=${item.name},
            name2=${item.name2}
        </set>
        where id = ${item.id}
    </foreach>      
</update>
```

但Mybatis映射文件中的sql语句默认是不支持以" ; " 结尾的，也就是不支持多条sql语句的执行。

所以需要在连接mysql的url上加 &allowMultiQueries=true 这个才可以执行。



## 4.MyBatis映射介绍



|          | JdbcType      | Oracle         | MySql              |
| :------- | :------------ | :------------- | ------------------ |
| JdbcType | ARRAY         |                |                    |
| JdbcType | BIGINT        |                | BIGINT             |
| JdbcType | BINARY        |                |                    |
| JdbcType | BIT           |                | BIT                |
| JdbcType | BLOB          | BLOB           | BLOB               |
| JdbcType | BOOLEAN       |                |                    |
| JdbcType | CHAR          | CHAR           | CHAR               |
| JdbcType | CLOB          | CLOB           | 修改为TEXT         |
| JdbcType | CURSOR        |                |                    |
| JdbcType | DATE          | DATE           | DATE               |
| JdbcType | DECIMAL       | DECIMAL        | DECIMAL            |
| JdbcType | DOUBLE        | NUMBER         | DOUBLE             |
| JdbcType | FLOAT         | FLOAT          | FLOAT              |
| JdbcType | INTEGER       | INTEGER        | INTEGER            |
| JdbcType | LONGVARBINARY |                |                    |
| JdbcType | LONGVARCHAR   | LONG VARCHAR   |                    |
| JdbcType | NCHAR         | NCHAR          |                    |
| JdbcType | NCLOB         | NCLOB          |                    |
| JdbcType | NULL          |                |                    |
| JdbcType | NUMERIC       | NUMERIC/NUMBER | NUMERIC/           |
| JdbcType | NVARCHAR      |                |                    |
| JdbcType | OTHER         |                |                    |
| JdbcType | REAL          | REAL           | REAL               |
| JdbcType | SMALLINT      | SMALLINT       | SMALLINT           |
| JdbcType | STRUCT        |                |                    |
| JdbcType | TIME          |                | TIME               |
| JdbcType | TIMESTAMP     | TIMESTAMP      | TIMESTAMP/DATETIME |
| JdbcType | TINYINT       |                | TINYINT            |
| JdbcType | UNDEFINED     |                |                    |
| JdbcType | VARBINARY     |                |                    |
| JdbcType | VARCHAR       | VARCHAR        | VARCHAR            |

## 5.更新时间字段按照年月日时分秒格式 更新为当前时间

mybatis在mysql 更新update 操作 

```mysql
update goods_msg SET create_date = DATE_FORMAT(NOW(),'%Y-%m-%d %H:%m:%s') WHERE uid = '6183b000-e7b3-4f38-8943-c9f170bd2d80'
```

