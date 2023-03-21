---
title: Mybatis学习笔记
description: 尚硅谷版Mybatis学习笔记
date: 2022-11-17 17:00:00
updated: 
tags:
  - Java
categories:
  - Java
cover: https://yimoorua-img.oss-cn-chengdu.aliyuncs.com/images/AgAAC6u-TVO-6GCp-41Pb5VAdprXFwlM.png
abbrlink: 
feature: true
---
# 尚硅谷版Mybatis学习笔记

## 一、Mybatis介绍

### 什么是Mybatis

答：1、Mybatis是一个半ORM（对象关系映射）框架，它内部封装了JDBC，开发时只需要关注SQL语句本身，不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。程序员直接编写原生态sql，可以严格控制sql执行性能，灵活度高。

2、MyBatis可以使用XML或注解来配置和映射原生信息，将POJO映射成数据库中的记录，避免了几乎所有的JDBC代码和手动设置参数以及获取结果集。

3、通过xml文件或注解的方式将要执行的各种statement配置起来，并通过java对象和statement中sql的动态参数进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射为java对象并返回。（从执行sql到返回result的过程）。

### Mybatis与Hibernate的区别

答：1、Mybatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句。

2、Mybatis直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套sql映射文件，工作量大。

3、Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率。

### 为什么使用Mybatis

答：1、基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用。

2、与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接；

3、很好的与各种数据库兼容（因为 MyBatis 使用 JDBC 来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）。

4、能够与Spring很好的集成；

5、提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。

### Mybatis下载地址

URL:Https://github.com/mybatis/mybatis-3

## 二、Mybatis测试使用

### 按官方文档简单使用Mybatis流程

1.导入jar包：mybatis-3.4.1.jar & mysql-connector-java.jar & log4j.jar

2.创建与数据表字段名对应的实体类

```java
public class Goods {
private Integer goodsId;
private String title;
private String subTitle;
private Float originalCost;
private Float currentPrice;
private Float discount;
private Integer isFreeDelivery;
private Integer categoryId;
...
}
```

3.创建全局配置文件Mybatis-Config.xml

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/babytun?useUnicode=true&amp;characterEncoding=utf-8"/>
                <property name="username" value="root"/>
                <property name="password" value="1234567890"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!-- 将我们写好的sql映射文件（GoodsMapper.xml）一定要注册到全局配置文件（Mybatis-Config.xml）中 -->
        <mapper resource="Mapper/GoodsMapper.xml"/>
    </mappers>
</configuration>
```

4.创建Mapper映射文件

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.imooc.POJO.GoodsMapper">
    <!--
     接口式编程：
     namespace:名称空间;指定为接口的全类名
     id：唯一标识
     resultType：返回值类型
     #{id}：从传递过来的参数中取出id值
-->
    <select id="selectGoods" resultType="com.imooc.POJO.Goods">
        select goods_id goodsId,title title,sub_title subTitle,original_cost originalCost,current_price currentPrice,discount discount,is_free_delivery isFreeDelivery,category_id categoryId
        from t_goods
        where goods_id = #{id}
    </select>
</mapper>
```

4.创建测试类（1.Mapper命名空间+id值；2.接口式编程（推荐））

```java
/**
 * 1、接口式编程
 *     原生：       Dao       ====>  DaoImpl
 *     mybatis：   Mapper ====>  xxMapper.xml
 *
 * 2、SqlSession代表和数据库的一次会话；用完必须关闭；
 * 3、SqlSession和connection一样她都是非线程安全。每次使用都应该去获取新的对象。
 * 4、mapper接口没有实现类，但是mybatis会为这个接口生成一个代理对象。
 *        （将接口和xml进行绑定）
 *        EmployeeMapper empMapper = sqlSession.getMapper(EmployeeMapper.class);
 * 5、两个重要的配置文件：
 *        mybatis的全局配置文件：包含数据库连接池信息，事务管理器信息等...系统运行环境信息
 *        sql映射文件：保存了每一个sql语句的映射信息：
 *                 将sql抽取出来。
 *
 *
 * @author Yimoo
 *
 */
public class mybatisTest {
    public SqlSessionFactory getSqlSessionFactory() throws IOException{
        String resource="Mybatis-Config.xml";
        InputStream inputStream=Resources.getResourceAsStream(resource);
        return new SqlSessionFactoryBuilder().build(inputStream);
    }

    /**
     * 1、根据xml配置文件（全局配置文件）创建一个SqlSessionFactory对象 有数据源一些运行环境信息
     * 2、sql映射文件；配置了每一个sql，以及sql的封装规则等。
     * 3、将sql映射文件注册在全局配置文件中
     * 4、写代码：
     *        1）、根据全局配置文件得到SqlSessionFactory；
     *        2）、使用sqlSession工厂，获取到sqlSession对象使用他来执行增删改查
     *           一个sqlSession就是代表和数据库的一次会话，用完关闭
     *        3）、使用sql的唯一标志来告诉MyBatis执行哪个sql。sql都是保存在sql映射文件中的。
     *
     * @throws IOException
     *
     */
    @Test
    public void demo1() throws IOException {
        // 2、获取sqlSession实例，能直接执行已经映射的sql语句
        // sql的唯一标识：statement Unique identifier matching the statement to use.
        // 执行sql要用的参数：parameter A parameter object to pass to the statement.
        SqlSessionFactory sqlSessionFactory=getSqlSessionFactory();
        SqlSession sqlSession=sqlSessionFactory.openSession();
         try{
             Goods goods=sqlSession.selectOne("com.imooc.POJO.GoodsMapper.selectGoods",800);
             System.out.println(goods);
         }finally {
             sqlSession.close();
         }
    }
  
  @Test
    public void demo2() throws IOException {
        SqlSessionFactory sqlSessionFactory=getSqlSessionFactory();
        SqlSession sqlSession=sqlSessionFactory.openSession();
        try{
            // 3、获取接口的实现类对象
            //会为接口自动的创建一个代理对象，代理对象去执行增删改查方法
            GoodsMapper goodsMapper=sqlSession.getMapper(GoodsMapper.class);
            Goods goods=goodsMapper.selectGoods(900);
            System.out.println(goods);
          //输出：Goods{goodsId=900, title='三星 2016版 Galaxy On7（G6100）全网通4G 3GB+32G 钛岩黑 手机',          subTitle='美囤自营，品质保证！5.5英寸屏，3GB+32GB，双账户应用工作生活两不误！', originalCost=1799.0, currentPrice=1299.0, discount=0.722068, isFreeDelivery=1, categoryId=30}
            //自动创建代理
            System.out.println(goodsMapper.getClass());//输出：class com.sun.proxy.$Proxy6
        }finally {
            sqlSession.close();
        }
    }
}
```

### 如何让实体类的属性名与数据库字段名一一对应

1.在mapper文件中sql语句处为字段名设置别名，别名为实体类的属性名

```java
<select id="selectGoods" resultType="com.imooc.POJO.Goods">
    select goods_id goodsId,title title,sub_title subTitle,original_cost originalCost,current_price currentPrice,discount discount,is_free_delivery isFreeDelivery,category_id categoryId
    from t_goods
    where goods_id = #{id}
</select>
```

2.在全局配置文件中配置mapUndertocamleCase=true，前提是必须属性名与字段名为驼峰命名关系

```xml
<settings>
    <setting name="mapUnderScoretoCamleCase" value=""/>
</settings>
```

## 三、全局配置文件的标签

configuration [mybatis-3.5.9.pdf官方文档](/Users/yimoorua/Downloads/mybatis-3.5.9/mybatis-3.5.9.pdf ) 

• properties
• settings
• typeAliases
• typeHandlers
• objectFactory（了解）
• plugins
• environments
      • environment
           • transactionManager
               • dataSource
               • databaseIdProvider
• mappers

### properties属性（属性文件）

```xml
<!--
		1、mybatis可以使用properties来引入外部properties配置文件的内容；
		resource：引入类路径下的资源
		url：引入网络路径或者磁盘路径下的资源
	  -->
	<properties resource="dbconfig.properties"></properties>
```

dbconfig.properties文件
```xml
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/babytun?useUnicode=true&amp;characterEncoding=utf-8
jdbc.username=root
jdbc.password=1234567890

orcl.driver=oracle.jdbc.OracleDriver
orcl.url=jdbc:oracle:thin:@localhost:1521:orcl
orcl.username=scott
orcl.password=123456
```

修改environment标签下的properties的value值
```xml
<property name="driver" value="${jdbc.driver}"/>
<property name="url" value="${jdbc.url}"/>
<property name="username" value="${jdbc.username}"/>
<property name="password" value="${jdbc.password}"/>
```

### settings设置（自动开启驼峰命名规则）

这是Mybatis中极为重要的调整设置，它们会改变Mybatis的运行时行为

mapUnderscoreToCamelCase：是否开启自动驼峰命名规则映射，即从经典数据库列名A_COLUMN到经典Java属性名aColumn的类似映射

```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

### typeAliases别名处理器（java类型起别名）

类型别名是为Java类型设置一个短的名字，方便我们引用某个类

```xml
<typeAliases>
<typeAlias type="com.imooc.POJO.Goods" alias="goods"/>
</typeAliases>
```

类很多的情况下，可以批量设置这个包下的每一个类创建一个默认的别名，就是简单类名小写。

```xml
<typeAliases>
<!--        别名不区分大小写-->
        <package name="com.imooc.POJO"/>
    </typeAliases>
```

也可以使用注解@Alias注解为其指定一个别名（注意只有使用批量起别名时，才可以使用注解起别名）

```xml
<typeAliases>
<!--        别名不区分大小写-->
        <package name="com.imooc.POJO"/>
    </typeAliases>
```

```java
@Alias("goods")
public class Goods {
...
}
```

MyBatis已经为许多常见的 Java 类型内建了相应的类型别名。它们都是大小写不敏感的，我们在起别名的时候千万不要占用已有的别名

| 别名 | 映射的类型 | 别名 | 映射的类型 | 别名 | 映射的类型 |
| :--: | :--------: | :--: | :--------: | :--: | :--------: |
|_byte | byte | string | String | date | Date |
| _long | long | byte | Byte | decimal | BigDecimal |
|_short | short | long | Long | bigdecimal | BigDecimal |
|_int | int | short | Short | object | Object |
|_integer | int | int | Integer | map | Map |
| _double | double  | double | Double | hashmap | HashMap |
|_float | float | float | Float | list | List |
| _boolean | boolean | boolean | Boolean | arraylist | ArrayList |
|  |  |  |  | collection | Collection|
| |   |   |   |  iterator | Iterator|

### typeHandlers类型处理器

无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。

| 类型处理器 | Java 类型 | JDBC 类型 |
| ---------- | --------- | --------- |
| BooleanTypeHandler | java.lang.Boolean, boolean | 数据库兼容的 BOOLEAN |
|ByteTypeHandler |java.lang.Byte, byte | 数据库兼容的 NUMERIC 或 BYTE |
| ShortTypeHandler | java.lang.Short, short | 数据库兼容的 NUMERIC 或 SHORT INTEGER |
|IntegerTypeHandler|java.lang.Integer, int | 数据库兼容的 NUMERIC 或 INTEGER |
|LongTypeHandler | java.lang.Long, long|数据库兼容的 NUMERIC 或 LONG INTEGER |
|FloatTypeHandler |java.lang.Float, float | 数据库兼容的 NUMERIC 或 FLOAT |
|DoubleTypeHandler | java.lang.Double, double |数据库兼容的 NUMERIC 或 DOUBLE |
|BigDecimalTypeHandler | java.math.BigDecimal | 数据库兼容的 NUMERIC 或 DECIMAL |
| StringTypeHandler |java.lang.String |CHAR, VARCHAR |

**日期类型的处理**
1、日期和时间的处理，JDK1.8以前一直是个头疼的问题。我们通常使用JSR310规范领导者Stephen Colebourne
创建的Joda-Time来操作。1.8已经实现全部的JSR310规范了。
2、日期时间处理上，我们可以使用MyBatis基于JSR310（Date and Time API）编写的各种日期时间类型处理器。
3、MyBatis3.4以前的版本需要我们手动注册这些处理器，以后的版本都是自动注册的。


**自定义类型处理器**
我们可以重写类型处理器或创建自己的类型处理
器来处理不支持的或非标准的类型。 
步骤：
1）、实现org.apache.ibatis.type.TypeHandler接口或者继承org.apache.ibatis.type.BaseTypeHandler
2）、指定其映射某个JDBC类型（可选操作）
3）、在mybatis全局配置文件中注册

### plugins插件（先学习Mybatis工作原理才能理解）

插件是MyBatis提供的一个非常强大的机制，我们可以通过插件来修改MyBatis的一些核心行为。插件通过动态代理机制，可以介入四大对象的任何一个方法的执行。后面会有专门的章节我们来介绍mybatis运行原理以及插件

**四大对象**

• **Executor** (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)

• **ParameterHandler** (getParameterObject, setParameters) 

• **ResultSetHandler** (handleResultSets, handleOutputParameters) 

• **StatementHandler** (prepare, parameterize, batch, update, query) 

### environments环境

```xml
<environments default="">
    <environment id="">
        <transactionManager type=""/>
        <dataSource type="">
            <property name="driver" value=""/>
            <property name="url" value=""/>
            <property name="username" value=""/>
            <property name="password" value=""/>
        </dataSource>
    </environment>
</environments>
```

1、`<environments default=""/>`

• MyBatis可以配置多种环境，比如开发、测试和生产环境需要有不同的配置。 

• 每种环境使用一个environment标签进行配置并指定唯一标识符

• 可以通过environments标签中的default属性指定一个环境的标识符来快速的切换环境

2、`<environment id="”><transactionManager type=""/><dataSource type=""></dataSource></environment>`

• id：指定当前环境的唯一标识

• transactionManager、和dataSource都必须有

3、`<transactionManager type="JDBC|MANAGED|自定义">`

• JDBC：使用了 JDBC 的提交和回滚设置，依赖于从数据源得到的连接来管理事务范围。JdbcTransactionFactory

• MANAGED：不提交或回滚一个连接、让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）。 ManagedTransactionFactory

• 自定义：实现TransactionFactory接口，type=全类名/别名

4、`<dataSource type="UPOOLED|POOLED|JNDI|自定义">...</dataSource>`

• UNPOOLED：不使用连接池，UnpooledDataSourceFactory

• POOLED：使用连接池， PooledDataSourceFactory

• JNDI： 在EJB 或应用服务器这类容器中查找指定的数据源

• 自定义：实现DataSourceFactory接口，定义数据源的获取方式。

• 实际开发中我们使用Spring管理数据源，并进行事务控制的配置来覆盖上述配置

### databaseIdProvider环境（数据库的移植性）

MyBatis 可以根据不同的数据库厂商执行不同的语句。 


• Type： DB_VENDOR

– 使用MyBatis提供的VendorDatabaseIdProvider解析数据库厂商标识。也可以实现DatabaseIdProvider接口来自定义。 

• Property-name：数据库厂商标识

• Property-value：为标识起一个别名，方便SQL语句使用databaseId属性引用


**DB_VENDOR**

– 会通过 **DatabaseMetaData#getDatabaseProductName()** 返回的字符串进行设置。由于通常情况下这个字符串都非常长而且相同产品的不同版本会返回不同的值，所以最好通过设置属性别名来使其变短 

• MyBatis匹配规则如下：

1、如果没有配置databaseIdProvider标签，那么databaseId=null

2、如果配置了databaseIdProvider标签，使用标签配置的name去匹配数据库信息，匹配上设置databaseId=配置指定的值，否则依旧为null

3、如果databaseId不为null，他只会找到配置databaseId的sql语句

4、MyBatis 会加载不带 databaseId 属性和带有匹配当前数据库databaseId 属性的所有语句。如果同时找到带有 databaseId 和不带databaseId 的相同语句，则后者会被舍弃。

### mapper映射（将sql映射注册到全局配置中）

```xml
<mappers>
    <!--resource：引用类路径下的sql映射文件-->
    <mapper resource="Mapper/GoodsMapper.xml"/>
    <!--url：引用网络路径或者磁盘路径下的sql映射文件-->
    <mapper url="file://D://GoodsMapper.xml"/>
    <!--class：引用（注册）接口
        1、有sql映射文件，映射文件名必须和接口同名，并且放在与接口同一目录下
        2、没有sql映射文件，所有sql都是利用注解写在接口上
        推荐：比较重要的DAO接口我们写sql映射文件
             不重要，简答的DAO接口为了快速开发可以使用注解-->
    <mapper class="com.imooc.DAO.GoodsMapper"/>
    <!--package：批量注解，自动扫描包下映射文件-->
    <package name="com.imooc.DAO"/>
</mappers>
```

## 四、Mybatis映射文件

映射文件指导着MyBatis如何进行数据库增删改查，有着非常重要的意义；

映射文件的元素，如下：

•cache –命名空间的二级缓存配置
•cache-ref – 其他命名空间缓存配置的引用。
•resultMap – 自定义结果集映射
•parameterMap – 已废弃！老式风格的参数映射
•sql –抽取可重用语句块。
•insert – 映射插入语句
•update – 映射更新语句
•delete – 映射删除语句
•select – 映射查询语句

### insert、update、delete元素

测试增删改

1、parameterType：参数类型，可以省略

2、mybatis允许增删改直接定义以下类型返回值

Integer、long、Boolean、void

3、需要手动提交commit

手动提交：sqlSessionFactory,openSession();

自动提交：sqlSessionFactory.openSession(true);

### 主键生成方式

1、mysql支持自增主键，自增主键值的获取，mybatis是利用statement.getGenreatedKeys()；

**useGeneratedKeys=”true”**：使用自增主键获取主键值策略

keyProperty：指定对应的主键属性（对应的JavaBean的属性名），也就是mybatis获取到主键值以后，将这个值封装给javaBean的对应的属性

```xml
<insert id="insertGoods" useGeneratedKeys="true" keyProperty="goodsId" databaseId="mysql">
    insert into t_goods(title,sub_title,original_cost,current_price,discount,is_free_delivery,category_id)
    values (#{title},#{subTitle},#{originalCost},#{currentPrice},#{discount},#{isFreeDelivery},#{categoryId})
</insert>
```

 2、oracle不支持自增主键，它使用的是序列来模拟自增，每次插入数据的主键是从序列中拿到的值。

获取主键值可以使用 **selectKey** 子元素：selectKey 元素将会首先运行，id 会被设置，然后插入语句会被调用

Order="BEFORE"：当前sql在插入sql之前运行

```xml
<insert id="insertEmployee" databaseId="oracle">
    <selectKey keyProperty="id" order="BEFORE" resultType="Integer">
        select EMPLOYEES_SEQ.nextval from dual
    </selectKey>
    insert into employee(EMPLOYEE_ID,LAST_NAME,EMAIL)
    values (#{id},#{lastName},#{email})
</insert>
```

**执行流程：**

先运行selectKey查询id的sql，查出id值封装给javaBean的id属性

在运行插入的sql；就可以取出id属性对应的值

selectKey标签中各属性的作用：


### 参数处理

1、单参数：没有限制，通过接口方法直接传参
2、多个参数：mybatis会做特殊处理，多个参数被封装成一个map
三种方法：
• 使用#{param1},#{param2}...

• （命名参数）在接口方法参数前指定(@Param("属性名") Object 属性名,@Param("属性名") Object 属性名...)
多个参数会封装成map，key==》@param("属性名")，value ==》方法参数值

• 如果多个参数正好是我们逻辑业务的数据模型，我们可以直接传入POJO
#{属性名}：取出传入的POJO的属性值

• 如果多个参数不是业务模型中的数据，没有对应的POJO，不经常使用为了方便，也可以传入map
#{key}：取出map中对应的值
如果多个参数不是业务模型中的数据，但经常使用，推荐来编写一个TO（Transfer Object）数据传输

 Page{
Int index;
int size;
}

========================思考================================	
public Employee getEmp(@Param("id")Integer id,String lastName);
	取值：id==>#{id/param1}   lastName==>#{param2}

public Employee getEmp(Integer id,@Param("e")Employee emp);
	取值：id==>#{param1}    lastName===>#{param2.lastName/e.lastName}

##特别注意：如果是Collection（List、Set）类型或者是数组，
		 也会特殊处理。也是把传入的list或者数组封装在map中。
			key：Collection（collection）,如果是List还可以使用这个key(list)
				数组(array)
public Employee getEmpById(List<Integer> ids);
	取值：取出第一个id的值：   #{list[0]}
	
========================结合源码，mybatis怎么处理参数======================
**总结：参数多时会封装map，为了不混乱，我们可以使用@Param来指定封装时使用的key；
#{key}就可以取出map中的值；**

(@Param("id")Integer id,@Param("lastName")String lastName);
ParamNameResolver解析参数封装map的；
//1、names：{0=id, 1=lastName}；构造器的时候就确定好了
      1.获取每个标了param注解的参数的@param的值：id，lastName，赋值给name;
      2.每次解析一个参数给map中保存信息：（key：参数索引，value：name的值）
                name的值：
                   标注了param注解：注解的值
                    没有标注：
                        1.全局配置：useActualParamName（jdk1.8）：name=参数名
                        2.name=map.size();相当于当前的元素的索引
                        {0=id,1=lastName,2=2}

### #{}与${}的区别

相同点：
都可以获取map中的值或者pojo对象的属性的值

不同点：
1.取值：#{}会用占位符预编译PreparedStatement到SQL中，${}会用字符串拼接SQL
2.安全性：#{}能预防SQL注入

${}的用处：原生jdbc不支持占位符的地方我们就可以使用${}进行取值
比如分表、排序...：按照年份分表拆分
             select * from ${year}_salary where xxx;
             select *  from tbl_employee order by ${f_name} ${order}

**#{}其他用法：**
   规定一些参数的规则：
   javaType、==jdbctype==、mode（存储过程）、numericScale、resultType、typeHandler、jdbcTypeName、expression（未来准备支持的功能）

   jdbcType通常需要在某种特定的条件下被设置：
   在我们数据为null的时候，有些数据库可能不能被识别mybatis对null的默认处理。比如Oracle（报错）；
   JdbcType OTHER：无效的类型；因为mybatis对所有的null都映射的是原生Jdbc的OTHER类型，orcal不认识
   由于全局配置：jdbcTypeForNull=OTHER；oracle不支持；两种解决办法：
   1、在对应sql参数中书写#{email,jdbcType=OTHER}
   2、在全局配置中设置
      <settings>
         <setting name="jdbcTypeForNull" value="NULL"/>
      </settings>

### select元素

Select元素来定义查询操作。

• Id：唯一标识符。– 用来引用这条语句，需要和接口的方法名一致

• parameterType：参数类型。 – 可以不传，MyBatis会根据TypeHandler自动推断

• resultType：返回值类型。 – 别名或者全类名，如果返回的是集合，定义集合中元素的类型。不能和resultMap同时使用

1、List集合返回值的查询方法：

```xml
public List<Goods> getGoodsByTitleLike(String title);

<select id="getEmpsByTitleLike" resultType="com.imooc.POJO.Goods">
        select * from t_goods where title like #{title}
    </select>
```
控制台输出内容：
```txt
DEBUG 05-14 17:50:44,942 ==>  Preparing: select * from t_goods where title like ?  (BaseJdbcLogger.java:137) 
DEBUG 05-14 17:50:44,967 ==> Parameters: %分娩%(String)  (BaseJdbcLogger.java:137) 
DEBUG 05-14 17:50:44,985 <==      Total: 2  (BaseJdbcLogger.java:137) 
北京和睦家医院自然分娩+无痛套餐（宝宝树专享，限定预产期2017年9月30日前）
北京和睦家医院自然分娩+无痛套餐（宝宝树专享，限定预产期2017年9月30日前）
```

2、Map集合返回值的查询方法：

a.单条记录封装

```xml

public Map<String,Object> getGoodsByIdReturnMap(Integer goodsId);

<select id="getGoodsByIdReturnMap" resultType="map">
        select * from t_goods where goods_id=#{goodsId}
    </select>
```
```txt
DEBUG 05-14 17:52:51,885 ==>  Preparing: select * from t_goods where goods_id=?  (BaseJdbcLogger.java:137) 
DEBUG 05-14 17:52:51,909 ==> Parameters: 800(Integer)  (BaseJdbcLogger.java:137) 
DEBUG 05-14 17:52:51,926 <==      Total: 1  (BaseJdbcLogger.java:137) 
{original_cost=279.0, sub_title=德国原产 欧洲直邮 德国 Hipp喜宝 益生菌 奶粉, is_free_delivery=1, category_id=46, goods_id=800, discount=0.892473, current_price=249.0, title=喜宝HiPP 【欧洲直邮】德国Hipp喜宝益生菌奶粉2段 600g*2}
```
b.多条记录封装，map的键为记录的主键，map的值为记录的值
```xml
  @MapKey("goodsId") //告诉mybatis封装这个Map用哪个属性作为key
    public Map<Integer,Goods> getGoodsByTitleLikeReturnMap(String title);

  <select id="getGoodsByTitleLikeReturnMap" resultType="com.imooc.POJO.Goods">
        select * from t_goods where title like #{title}
    </select>
```
控制台输出内容：
```txt
DEBUG 05-14 18:07:17,480 ==>  Preparing: select * from t_goods where title like ?  (BaseJdbcLogger.java:137) 
DEBUG 05-14 18:07:17,508 ==> Parameters: %分娩%(String)  (BaseJdbcLogger.java:137) 
DEBUG 05-14 18:07:17,525 <==      Total: 2  (BaseJdbcLogger.java:137) 
{
752=Goods{goodsId=752, title='北京和睦家医院自然分娩+无痛套餐（宝宝树专享，限定预产期2017年9月30日前）', subTitle='24小时售前咨询电话：400-891-9191', originalCost=76000.0, currentPrice=47880.0, discount=0.63, isFreeDelivery=1, categoryId=56}, 
1264=Goods{goodsId=1264, title='北京和睦家医院自然分娩+无痛套餐（宝宝树专享，限定预产期2017年9月30日前）', subTitle='24小时售前咨询电话：400-891-9191', originalCost=76000.0, currentPrice=47880.0, discount=0.63, isFreeDelivery=1, categoryId=46}
}
```

#### resultType属性

1、全局setting设置

– autoMappingBehavior默认是PARTIAL，开启自动映射的功能。唯一的要求是列名和javaBean属性名一致

– 如果autoMappingBehavior设置为null则会取消自动映射

– 数据库字段命名规范，POJO属性符合驼峰命名法，如A_COLUMNaColumn，我们可以开启自动驼峰命名规则映射功能，mapUnderscoreToCamelCase=true。

2.自定义resultMap，实现高级结果集映射

**等效于resultType的实例如下：**

```xml
//接口方法
public Goods selectGoods(Integer id);

<!--自定义某个javaBean的封装规则
     type：自定义规则-->
    <resultMap id="myGoods" type="com.imooc.POJO.Goods">
        <!--column字段名，property属性名-->
        <id column="goods_id" property="goodsId"/>
        <result column="title" property="title"/>
        <result column="sub_title" property="subTitle"/>
        <result column="original_cost" property="originalCost"/>
        <result column="current_price" property="currentPrice"/>
        <result column="discount" property="discount"/>
        <result column="is_free_delivery" property="isFreeDelivery"/>
        <result column="category_id" property="categoryId"/>
    </resultMap>
    <select id="selectGoods" resultMap="myGoods">
         select * from t_goods where goods_id=#{goodsId}
    </select>
```

控制台输出如下：（实现效果跟自动resultType=Goods自动封装一样，但支持高级自定）
```txt
DEBUG 05-14 18:33:51,774 ==>  Preparing: select * from t_goods where goods_id=?  (BaseJdbcLogger.java:137) 
DEBUG 05-14 18:33:51,807 ==> Parameters: 800(Integer)  (BaseJdbcLogger.java:137) 
DEBUG 05-14 18:33:51,826 <==      Total: 1  (BaseJdbcLogger.java:137) 
Goods{goodsId=800, title='喜宝HiPP 【欧洲直邮】德国Hipp喜宝益生菌奶粉2段 600g*2', subTitle='德国原产 欧洲直邮 德国 Hipp喜宝 益生菌 奶粉', originalCost=279.0, currentPrice=249.0, discount=0.892473, isFreeDelivery=1, categoryId=46}
```

#### association的三种使用方法

1.result

2.分步查询

3.延迟加载属性

每次查询Goods对象时，总是把对应id的detail信息查询出来。使用lazyLoadingEnable属性我们想要在需要时在查询出来

lazyLoadingenabled：true，开启延迟加载，按需查询

aggressiveLazyLoading：false，开启按需查询，true，关闭按需查询，全部查询出来

```xml
<setting name="lazyLoadingEnabled" value="true"/>
<setting name="aggressiveLazyLoading" value="false"/>
```

4.多对一查询

用N的一方GoodsDetail查询Goods的信息

代码实例如下

```xml
//GoodsMapper.xml
<select id="selectGoodsById" resultType="com.imooc.POJO.Goods">
        select *
        from t_goods
        where goods_id = #{goodsId}
    </select>

//GoodsDetailMapper.xml
<resultMap id="manyToOne" type="com.imooc.POJO.GoodsDetail">
     <id column="gd_id" property="gdId"/>
    <association property="goods" column="goods_id" select="com.imooc.DAO.GoodsMapper.selectGoodsById"/>
</resultMap>
<select id="selectGoodsByAsscoiation" resultMap="manyToOne">
    select * from t_goods_detail where goods_id=#{goodsId}
</select>
```

一对多查询（collection）

1的一方为Goods，n的一方为GoodsDetail，查询Goods的同时查询GoodsDetail对应id信息


代码实例如下：

```java
//1：在Goods实体类里增加goodsDet
public class Goods {
...
private List<GoodsDetail> goodsDetails;
...
}

//N
public class GoodsDetail {
    private Integer gdId;
    private Integer godsId;
    private String gdPicUrl;
    private Integer gdOrder;
 }

//GoodsMapper接口
public interface GoodsMapper {
public List<Goods> getGoodsDetailByOneToMany(Integer goodsId);
}

//GoodsDetailMapper接口
public interface GoodsDetailMapper {
    public List<GoodsDetail> selectDetailById(Integer gdId);
}
```

```xml
<resultMap id="MyGoodsByOneToMany" type="com.imooc.POJO.Goods">
        <id column="goods_id" property="goodsId"/>
        <!--可以省略其他result设定，前提是javabean属性名与字段名一一对应-->
        <!--association定义关联对象的封装规则
           select：表名当前属性是调用select指定的方法查出的结果
           column="指定将那一列的值传给这个方法"
           使用select指定的方法传入column指定的参数值，并封装给property封装的值
        -->
   <collection property="goodsDetails" select="com.imooc.DAO.GoodsDetailMapper.selectDetailById" column="goods_id"/>
    </resultMap>
    <select id="getGoodsDetailByOneToMany" resultMap="MyGoodsByOneToMany">
        select * from t_goods where goods_id=#{goodsId}
    </select>

<!--GoodsDetail的SQL查询方法-->
<select id="selectDetailById" resultType="com.imooc.POJO.GoodsDetail">
    select * from t_goods_detail where goods_id = #{gdId}
</select>
```

## 五、动态SQL

- Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题

## if

- if标签可通过test属性（即传递过来的数据）的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之标签中的内容不会执行
- 在where后面添加一个恒成立条件`1=1`
 - 这个恒成立条件并不会影响查询的结果
   - 这个`1=1`可以用来拼接`and`语句，例如：当empName为null时
    - 如果不加上恒成立条件，则SQL语句为`select * from t_emp where and age = ? and sex = ? and email = ?`，此时`where`会与`and`连用，SQL语句会报错
      - 如果加上一个恒成立条件，则SQL语句为`select * from t_emp where 1= 1 and age = ? and sex = ? and email = ?`，此时不报错

```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="Emp">
	select * from t_emp where 1=1
	<if test="empName != null and empName !=''">
		and emp_name = #{empName}
	</if>
	<if test="age != null and age !=''">
		and age = #{age}
	</if>
	<if test="sex != null and sex !=''">
		and sex = #{sex}
	</if>
	<if test="email != null and email !=''">
		and email = #{email}
	</if>
</select>
```

## where

- where和if一般结合使用：
 - 若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字  
   - 若where标签中的if条件满足，则where标签会自动添加where关键字，并将条件最前方多余的and/or去掉  

```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="Emp">
	select * from t_emp
	<where>
		<if test="empName != null and empName !=''">
			emp_name = #{empName}
		</if>
		<if test="age != null and age !=''">
			and age = #{age}
		</if>
		<if test="sex != null and sex !=''">
			and sex = #{sex}
		</if>
		<if test="email != null and email !=''">
			and email = #{email}
		</if>
	</where>
</select>
```

- 注意：where标签不能去掉条件后多余的and/or

	```xml
	<!--这种用法是错误的，只能去掉条件前面的and/or，条件后面的不行-->
	<if test="empName != null and empName !=''">
	emp_name = #{empName} and
	</if>
	<if test="age != null and age !=''">
		age = #{age}
	</if>
	```

## trim

- trim用于去掉或添加标签中的内容  
- 常用属性
 - prefix：在trim标签中的内容的前面添加某些内容  
   - suffix：在trim标签中的内容的后面添加某些内容 
   - prefixOverrides：在trim标签中的内容的前面去掉某些内容  
   - suffixOverrides：在trim标签中的内容的后面去掉某些内容
- 若trim中的标签都不满足条件，则trim标签没有任何效果，也就是只剩下`select * from t_emp`

```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="Emp">
	select * from t_emp
	<trim prefix="where" suffixOverrides="and|or">
		<if test="empName != null and empName !=''">
			emp_name = #{empName} and
		</if>
		<if test="age != null and age !=''">
			age = #{age} and
		</if>
		<if test="sex != null and sex !=''">
			sex = #{sex} or
		</if>
		<if test="email != null and email !=''">
			email = #{email}
		</if>
	</trim>
</select>
```

```java
//测试类
@Test
public void getEmpByCondition() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
	List<Emp> emps= mapper.getEmpByCondition(new Emp(null, "张三", null, null, null, null));
	System.out.println(emps);
}
```


## choose、when、otherwise

- `choose、when、otherwise`相当于`if...else if..else`
- when至少要有一个，otherwise至多只有一个

```xml
<select id="getEmpByChoose" resultType="Emp">
	select * from t_emp
	<where>
		<choose>
			<when test="empName != null and empName != ''">
				emp_name = #{empName}
			</when>
			<when test="age != null and age != ''">
				age = #{age}
			</when>
			<when test="sex != null and sex != ''">
				sex = #{sex}
			</when>
			<when test="email != null and email != ''">
				email = #{email}
			</when>
			<otherwise>
				did = 1
			</otherwise>
		</choose>
	</where>
</select>
```

```java
@Test
public void getEmpByChoose() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
	List<Emp> emps = mapper.getEmpByChoose(new Emp(null, "张三", 23, "男", "123@qq.com", null));
	System.out.println(emps);
}
```


- 相当于`if a else if b else if c else d`，只会执行其中一个

## foreach

- 属性：  
 - collection：设置要循环的数组或集合  
   - item：表示集合或数组中的每一个数据  
   - separator：设置循环体之间的分隔符，分隔符前后默认有一个空格，如` , `
   - open：设置foreach标签中的内容的开始符  
   - close：设置foreach标签中的内容的结束符
- 批量删除

	```xml
	<!--int deleteMoreByArray(Integer[] eids);-->
	<delete id="deleteMoreByArray">
		delete from t_emp where eid in
		<foreach collection="eids" item="eid" separator="," open="(" close=")">
			#{eid}
		</foreach>
	</delete>
	```
	```java
	@Test
	public void deleteMoreByArray() {
		SqlSession sqlSession = SqlSessionUtils.getSqlSession();
		DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
		int result = mapper.deleteMoreByArray(new Integer[]{6, 7, 8, 9});
		System.out.println(result);
	}
	```

- 批量添加

	```xml
	<!--int insertMoreByList(@Param("emps") List<Emp> emps);-->
	<insert id="insertMoreByList">
		insert into t_emp values
		<foreach collection="emps" item="emp" separator=",">
			(null,#{emp.empName},#{emp.age},#{emp.sex},#{emp.email},null)
		</foreach>
	</insert>
	```
	```java
	@Test
	public void insertMoreByList() {
		SqlSession sqlSession = SqlSessionUtils.getSqlSession();
		DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
		Emp emp1 = new Emp(null,"a",1,"男","123@321.com",null);
		Emp emp2 = new Emp(null,"b",1,"男","123@321.com",null);
		Emp emp3 = new Emp(null,"c",1,"男","123@321.com",null);
		List<Emp> emps = Arrays.asList(emp1, emp2, emp3);
		int result = mapper.insertMoreByList(emps);
		System.out.println(result);
	}
	```


## 