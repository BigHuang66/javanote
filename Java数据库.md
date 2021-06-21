# MySQL

## 基本的一些操作

```mysql
# 创建
CREATE DATABASE [IF NOT EXISTS] `数据库名`;
# 删除
DROP DATABASE [IF EXISTS] `数据库名`;
# 选择
USE <数据库>;   
# 创建数据表
CREATE TABLE [IF NOT EXISTS] `table_name` (
    `column_name` column_type [属性] [索引] [注释],
	# ...
	)[表类型][表字符集][注释];  
# 删除数据表
DROP TABLE [IF EXISTS] `table_name` ;     
 
# 修改表
AlTER TABLE `旧表名` RENAME AS `新表名`; # 修改表名
ALTER TABLE `表名` ADD `字段名` 列属性; # 添加字段
ALTER TABLE `表名` MODIFY `字段名` 列类型;
ALTER TABLE `表名` CHANGE `旧字段名` `新字段名` 列属性； # 修改字段
ALTER TABLE `表名` DROP 字段名

# 查询                       
SELECT column_name,column_name
FROM table_name
[WHERE Clause]
[LIMIT N][ OFFSET M]
# 修改 更新
UPDATE table_name SET field1=new-value1, field2=new-value2
[WHERE Clause]
# 删除
DELETE FROM table_name [WHERE Clause]
# 插入数据
INSERT INTO table_name ( field1, field2,...fieldN )
                       VALUES
                       ( value1, value2,...valueN ); 
```

## 事务

> 什么是事务

* 事务就是将一组SQL语句放在同一批次内去执行
* 如果一个SQL语句出错，则该批次内的所有SQL都将被取消执行
* MySQL事务处理只支持InnoDB 和 BDB数据表类型

>  事务的ACID原则

原子性（Atomic）、一致性（Consist）、隔离性（Isolated）、持久化（Durable）

# JDBC

SUN公司为了简化 开发人员的（对不同数据库的统一）操作，提供了 Java操作数据库的规范，JDBC

规范的实现由具体的厂商去做~

对于开发人员来说，我们只需要掌握JDBC接口的操作即可

<img src="https://cdn.jsdelivr.net/gh/BigHuang66/picBed@main/img/20210521200347.png" alt="image-20210521200346958" style="zoom: 67%;" />

```java
public class JdbcTest {
    public static void main(String[] args) throws ClassNotFoundException {
        // 1. 加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver"); // 固定写法，反射加载驱动
        
        // 2. 用户信息和url
        String url = "jdbc:mysql（协议）://主机地址:端口号/数据库名?useUnicode=true&characterEncoding=utf8&useSSL=true&serverTimezone=GMT";
        String username = "root";
        String password = "123456";
        // 3. 连接成功，数据库对象
        Connection connection = DriverManager.getConnection(url, username, password);
        // 4.执行SQL的对象  Statement 执行sql的对象
        Statement statement = connection.createStatement();
        // 5.执行SQL的对象 去 执行 SQL
        String sql = "SELECT * FROM users";
        ResultSet resultSet = statement.executeQuery(sql); // 返回的结果集，结果集中封装了我们全部查询出来的结果
        while (resultSet.next()) {
            System.out.println("id = " + resultSet.getObject("id"));
            System.out.println("name = " + resultSet.getObject("name"));
        }
        // 6.释放连接
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

==Jdbc中的statement对象用于向数据库发送SQL语句，想完成对数据库的增删改查，只需要通过这个对象向数据库发送增删改查语句即可。==

> Statement 执行SQL的对象     PrepareStatement 执行SQL的对象

```java
statement.executeQuery();  // 查询操作   返回 ResultSet
statement.execute();       // 执行任何SQL
statement.executeUpdate(); // 更新、插入、删除。 都是使用这个，返回一个受影响的行数
```

> ResultSet 查询的结果集：封装了所有的查询结果

```java
resultSet.getObject(); //不知道列类型情况下
// 知道了就是用指定的类型
resultSet.getString();
resultSet.getFloat();
......
// 指针遍历
resultSet.beforeFirst();  // 移动到最前面
resultSet.afterLast();    // 移动到最后面
resultSet.next();         // 移动到下一个数据
resultSet.previous();     // 移动到前一行
resultSet.absolute(row);  // 移动到指定行
```

> 释放资源

```java
resultSet.close();
statement.close();
connection.close(); // 耗资源，需要关闭
```



> SQL 注入的问题

sql 存在漏洞，会被攻击导致数据泄漏



#### PreparedStatement对象

PreparedStatement 可以防止SQL注入。效率更好！

```java
Connection conn = JdbcUtils.getConnection(); // 自己封装的工具类
String sql = "insert into users(id, `NAME`, `PASSWORD`, `email`, `birthday`) values (?, ?, ?, ?, ?)";
PreparedStatement st = conn.prepareStatement(sql); // 预编译SQL
st.setInt(1, 4);
st.setString(2, "Hjc");
st.setString(3, "1000");
st.setString(4, "8988888@qq.com");
st.setDate(5, new java.sql.Date(new Date().getTime()));
int ist.
```





# MyBatis

官方文档： [mybatis – MyBatis 3 | 简介](https://mybatis.org/mybatis-3/zh/index.html)

## 1、简介

==MyBatis==是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。 实现sql和代码的分离，提高可维护性。

==持久化：==

* 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
* 内存：断电即失
* 数据库（jdbc），io文件持久化（浪费资源）

==持久层：==

* 完成持久化工作的代码块
* 层界限十分明显



## 2、第一个Mybatis程序

思路：搭建环境 --> 导入Mybatis --> 编写代码 --> 测试 ！ 

```mysql
CREATE DATABASE `mybatis`;

USE `mybatis`;

CREATE TABLE `user`(
`id` INT(20) NOT NULL PRIMARY KEY,
`name` VARCHAR(30) DEFAULT NULL,
`pwd` VARCHAR(30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `user`(`id`,`name`,`pwd`) VALUES
(1,'huang','123456'),
(2,'li','234567'),
(3,'liu','345678')

```

### 2.1 新建项目：

1. 新建一个普通的maven项目

2. 删除src目录

3. 导入maven依赖

   ```xml
   <!--导入依赖-->
       <dependencies>
           <!--mysql驱动-->
           <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.21</version>
           </dependency>
   
           <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.4.6</version>
           </dependency>
   
           <!--junit-->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.13</version>
           </dependency>
       </dependencies>
   
   	<!--在build中配置resources，来防止出现资源导出失败的问题-->
   	<build>
           <resources>
               <resource>
                   <directory>src/main/resources</directory>
                   <includes>
                       <include>**/*.properties</include>
                       <include>**/*.xml</include>
                   </includes>
                   <filtering>false</filtering>
               </resource>
               <resource>
                   <directory>src/main/java</directory>
                   <includes>
                       <include>**/*.properties</include>
                       <include>**/*.xml</include>
                   </includes>
                   <filtering>false</filtering>
               </resource>
           </resources>
       </build>
   ```

### 2.2 创建模块

* 编写mybatis的核心配置文件

```xml
<!--configuration核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=true&amp;serverTimezone=GMT"/>
                <property name="username" value="root"/>
                <property name="password" value="3000"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```

* 编写mybatis工具类

```java
// SqlSessionFactory
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}
```

### 2.3 编写代码

* 实体类 pojo

```java
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}

```

* Dao接口

```xml
public interface UserDao {
    List<User> getUserList();
}

```

* 接口实现类由原来的UserDaoImpl转变为一个Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.huang.dao.UserDao">
    <!--select查询语句-->
    <select id="getUserList" resultType="com.huang.pojo.User">
        select * from mybatis.user
    </select>
</mapper>
```

### 2.4 测试

* junit测试

```java
public class UserDaoTest {
    @Test
    public void test(){
        // 第一步：获得SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        // 方式一：getMapper
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> userList = userDao.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }

        // 关闭sqlSession
        sqlSession.close();
    }
}
```





## 3、CRUD

==增删改需要提交事务==  ： `sqlSession.commit()`



namespace中的包名要和Dao/mapper 接口的包名一致！



* 编写接口
* 编写对应的mapper中的sql语句
* 测试



Map传递参数，直接在sql中取出key即可！ 【parameterType="map"】

对象传递参数，直接在sql中取对象的属性即可！【parameterType=“Object】

只有一个基本类型参数的情况下，可以直接在sql中取到！

多个参数用Map，或者**注解！**

#### 模糊查询：

1. Java代码执行的时候，传递通配符%%

```java
List<User> userList = mapper.getUserLike("%李%");
```

2. 在sql拼接中使用通配符！

```mysql
select * from mybatis.user where name like "%"#{value}"%"
```



## 4、配置解析

#### 1、核心配置文件

```java
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
```

#### 2、环境配置（environment）

MyBatis 可以配置成适应多种环境，但是每个SqlSessionFactory实例只能选择一种环境。

Mybatis默认的事务管理器就是JDBC，连接池：POOLED



#### 3、属性（properties）

可以通过properties属性 来实现引用配置文件

这些属性都是可外部配置且可动态替换的：

* 既可以在典型的Java属性文件中配置 【db.properties】

* 亦可通过properties元素的子元素来传递

db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSL=true&serverTimezone=GMT
username=root
password=123456
```

在mybatis核心配置文件中，引入外部配置文件：

```xml
<properties resource="db.properties"/>
或者
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="root"/>
  <property name="password" value="123456"/>
</properties>
```

* 可以直接引入外部文件
* 可以在其中增加一些属性配置
* 如果两个文件有同一个字段，优先使用外部配置文件的！



#### 4、类型别名（typeAliases）

* 类型别名是为 Java 类型设置一个短的名字。
* 存在的意义仅在于用来减少类完全限定名的冗余

```xml
<typeAliases>
    <typeAlias type="com.huang.pojo.User" alias="User"/>
</typeAliases>
```

也可以指定一个包名，MyBatis会在包名下面搜索需要的 Java Bean，比如：

扫描实体类的包，它的默认别名就为这个类的类名，首字母小写！

```xml
<typeAliases>
    <package name="com.huang.pojo"/>
</typeAliases>
```

在实体类比较少的时候，使用第一种方式。如果实体类较多，使用第二种。

第一种可以自定义别名，第二种不行（但是可以通过在实体类上添加注解的方式自定义）

```java
@Alias("user")
public class User {}
```

#### 5、设置

这是MyBatis中极为重要的调整设置，他们会改变MyBatis

#### 6、通用配置

#### 7、映射器

  MapperRegistry：注册绑定我们的Mapper文件；

方式一：

```xml
<mappers>
    <mapper resource="com/huang/dao/UserMapper.xml"/>
</mappers>
```

方式二：使用class文件绑定注册

```xml
<mappers>
    <mapper class="com.huang.dao.UserMapper"/>
</mappers>
```

方式三：使用扫描包进行注入绑定：

```xml
<mappers>
    <package class="com.huang.dao"/>
</mappers>
```

注意点：

* 接口和他的Mapper配置文件必须同名！
* 接口和他的Mapper配置文件必须在同一个包下！

#### 8、生命周期和作用域

**SqlSessionFactoryBuilder:**

* 一旦创建了SqlSessionFactory，就不需要它了
* 一般设置为局部变量

**SqlSessionFactory：**

* 可以视为：数据连接池
* SqlSessionFactory 一旦被创建就应该在运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**

* 因此SqlSessionFactory的最佳作用域是应用作用域。
* 最简单的就是使用**单例模式**或者静态单例模式

SqlSession

* 连接到连接池的一个请求！
* SqlSession的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。
* 用完之后需要感觉关闭，否则资源被占用！



## 5、解决属性名和字段名不一致的问题

 解决方法：

* 起别名

```xml
<select id="getuserById" resultType="com.huang.pojo.User">
	select id,name,pwd as password from mybatis.user where id = #{id}
</select>
```

**resultMap     结果集映射**

ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。

```xml
<resultMap id="UserMap" type="User">
    <!==column数据库中的字段，property实体类中的属性==>
	<result column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="pwd" property="password"/>
</resultMap>

<select id="getuserById" resultMap="UserMap">
	select * from mybatis.user where id = #{id}
</select>
```



## 6、日志

#### 6.1 日志工厂

![image-20210609095157609](https://cdn.jsdelivr.net/gh/BigHuang66/picBed@main/img/20210609095157.png)

- SLF4J
- LOG4J【掌握】
- LOG4J2
- JDK_LOGGING
- COMMONS_LOGGING 
- STDOUT_LOGGING 【掌握】
- NO_LOGGING

在Mybatis 中具体使用哪个日志输出，在setting中设置！

```xml
<settings>
	<!--标准日志工厂实现-->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

![image-20210609095957648](https://cdn.jsdelivr.net/gh/BigHuang66/picBed@main/img/20210609095957.png)

#### 6.2、LOG4J

* LOG4J是Apache的一个开源项目，通过使用LOG4J，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件
* 可以控制每一条日志的输出格式
* 通过定义每一条日志信息的级别，我们能够更加细致地控制日志
* 通过配置文件进行灵活的配置，不需要修改应用的代码

1. 导入log4j的包

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2. log4j.properties

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/kuang.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

3. 配置log4j为日志的实现

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

4. 使用
   * 导包 import org.apache.log4j.Logger;
   * 日志对象，参数为当前类的class
   * 日志级别

```java
static Logger logger = Logger.getLogger(UserDaoTest.class)

logger.info("")
logger.debug("")
logger.error("")
```





## 7、分页

* 减少数据的处理量

**使用Limit分页** 

```sql
SELECT * from user limit startIndex,pageSize;
SELECT * from user limit n;       #[0,n]
```

使用Mybatis实现分页

1. 接口

```java
List<User> getUserByLimit(Map<String, Integer> map);
```

2. Mapper.xml

```xml
<select id="getUserByLimit" parameterType="map" resultMap="UserMap">
	select * from mybatis.user limit #{startIndex},#{pageSize}
</select>
```

3. 测试

```java
@Test
public void getUserByLimit() {
    // 获得SqlSession对象
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    
    Hashmap<String, Integer> map = new HashMap<String, Integer>();
    map.put("startIndex", 1);
    map.put("pageSize", 2);
    List<User> userList = mapper.getUserByLimit(map);
    for (User user : userList) {
        System.out.println(user);
    }

    // 关闭sqlSession
    sqlSession.close();
}
```



## 8、使用注解开发

1. 注解在接口上实现

```java
public interface UserMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  List<User> getUserById(int id);
}
```

2. 需要再核心配置文件中绑定接口！

```xml
<!--绑定接口-->
<mappers>
	<mapper class="com.huang.dao.UserMapper"/>
</mappers>
```

3. 测试

本质：反射机制实现

底层：动态代理！



#### 注解实现CRUD

在工具类创建的时候是先自动提交事务：

```java
public static SqlSession getSqlSession() {
	return sqlSessionFactory.openSession(true);
}
```

编写接口：

```java
public interface UserMapper {
    @Select("SELECT * FROM blog WHERE id = #{id}")
    List<User> getUserById(int id);
    
    // 方法存在多个参数，所有的参数前面必须加上@Param("id")注解
    @Select("select * from user where id = #{}")
    User getUserByID(@Param("id") int id);
    
    @Insert("insert into user(id,name,pwd) values (#{id},#{name},#{password})")
    int addUser(User user);
    
    @Delete("delete from user where id = #{uid}")
    int deleteUser(@Param("uid") int id);
    
    
}
```

【注意：必须将接口注册绑定到我们的核心配置文件中  mappers】

```xml
<mappers>
	<mapper class="com.huang.dao.UserMapper"/>
</mappers>
```



#### 关于@Param( ) 注解

* 基本类型的参数或者String类型，需要加上
* 引用数据类型不需要加
* 如果只有一个基本类型的话，可以忽略，但是建议大家都加上！
* 在SQL中引用的就是@Param(” “)中设定的属性名！

#{ } 可以防止注入问题，${ } 直接连接



## 9、Lombox





## 10、多对一处理





 ```xml
<!--
	1. 查询所有的学生信息
    2. 根据查询出来的学生的tid，寻找对应的老师！
-->
<select id="getStudent" resultMap="StudentTeacher">
	select * from student
</select>
<resultMap id="StudentTeacher" type="Student">
	<result property="id" column="id"/>
    <result property="name" column="name"/>
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>
<select id="getTeacher" resultType="Teacher">
	select * from teacher where id = #{id}
</select>
 ```



```xml

<select id="getStudent" resultMap="StudentTeacher">
	select s.id sid, s.name sname t.name tname
    from student s, teacher t
    where s.tid = tid
</select>
<resultMap id="StudentTeacher" type="Student">
	<result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
    	<result property="name" column="tname"/>
    </association>
</resultMap>
```



## 11、一对多处理

未学



面试高频：

* Mysql引擎
* InnoDB底层原理
* 索引
* 索引优化！



## 12、动态SQL

==动态SQL就是根据不同的条件生成不同的SQL语句==

```java
@SuppressWarnings("all") // 抑制警告
```



1. 导包
2. 编写配置文件
3. 编写实体类

```java
@Data
public class Blog {
    private int id;
    private String title;
    private String author;
    private Date createTime;
    private int views;
}
```

4. 编写实体类对应Mapper接口和 Mapper.XML 文件



#### IF where

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
	select * from mybatis.blog
    <where>
    	<if test="title != null">
    		title = #{title}
        </if>
        <if test="author != null">
            and author = #{author}
        </if>
    </where>
</select>
```



#### Choose  类似于case，只会执行一个

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
	select * from mybatis.blog
    <where>
        <choose>
        	<when test="title != null">
            	title = #{title}
            </when>
            <when test="author != null">
                and author = #{author}
            </when>
            <otherwise>
            	and views = #{views}
            </otherwise>
        </choose>
    </where>
</select>
```



#### set

用于动态更新语句的解决方案叫做set

set元素可以用于动态包含需要更新的列，而舍去其它的。

```xml
<update id="updateBlog" parameterType="map">
	update mybatis.blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    where id = #{author}
</update>
```



==所谓的动态SQL，本质还是SQL语句，只是我们可以在SQL层面，去执行一个逻辑代码==

#### Foreach

```xml
<select id="queryBlogForeach" parameterType="map" resultType="blog">
	select * from mybatis.blog
    <where>
    	<foreach collection="ids" item="id" open="and (" close=")" separator="or">
        	id = #{id}
        </foreach>
    </where>
</select>
```



 

#### SQL片段

将一些功能的部分抽取出来，方便复用！

1. 使用SQL标签抽取公共的部分

   ```xml
   <sql id="if-title-author">
   	<if test="title != null">
       	title = #{title}
       </if>
       <if test="author != null">
       	and author = #{author}
       </if>
   </sql>
   ```

2. 在需要使用的地方使用include标签引用即可

   ```xml
   <select id="queryBlogIF" parameterType="map" resultType="blog">
   	select * from mybatis.blog
       <where>
       	<include refid="if-title-author"/>
       </where>
   </select>
   ```

注意事项：

* 最好基于单表来定义SQL片段！
* 不要存在where标签，这样会降低复用性，尽量在片段中只使用 if 等



==动态SQL就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合==

建议：

* 先在Mysql中写出完整的SQL，再对应的去修改成为我们的动态的SQL实现

## 13、缓存

#### 13.1 简介

1. 什么是缓存[ Cache ]？
   * 存在内存中的临时数据
   * 将用户经常查询的数据放在缓存（内存）中，用户查询数据就不用从磁盘上（关系型数据库数据文件）查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题
2. 为什么使用缓存？
   * 减少和数据库的交互次数，减少系统开销，提供系统效率
3. 什么样的数据能使用缓存？
   * 经常查询并且不经常改变的数据。

#### 13.2 Mybatis缓存

* MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率
* MyBatis系统中默认定义了两级缓存：**一级缓存** 和 **二级缓存**
  * 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
  * 二级缓存需要手动开启和配置，踏实基于namespace级别的缓存

#### 13.3 一级缓存

* 一级缓存也叫本地缓存：SqlSession
  * 与数据库同一次会话期间查询到的数据会放在本地缓存中
  * 以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库

缓存失效的情况：

1. 查询不同的东西

2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存

   ![image-20210610212326551](https://cdn.jsdelivr.net/gh/BigHuang66/picBed@main/img/20210610212326.png)

3. 查询不同的Mapper.xml

4. 手动清理缓存！



小结：一级缓存默认是开启的，只在一次SqlSession中有效，也就是拿到连接 dao 关闭连接这个区间段！

#### 13.4、二级缓存

* 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
* 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；
* 工作机制
  * 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  * 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存二级缓存中；
  * 新的会话查询信息，就可以从二级缓存中获取内容；
  * 不同的mapper查出的数据会放在自己对应的缓存（map）中

步骤：

1. 开启全局缓存

   ```xml
   <!--显示的开启全局缓存-->
   <setting name="cacheEnabled" value="true"/>
   ```

2. 在要使用二级缓存的Mapper中开启

   ```xml
   <cache/>
   或者自定义参数
   <cache eviction="FIFO"
          flushInterval="6000"
          size="512"
          readOnly="true"/>
   ```

3. 测试

   ```java
   @Test
   public void getUserByLimit() {
       // 获得SqlSession对象
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       SqlSession sqlSession2 = MybatisUtils.getSqlSession();
       
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       User user = mapper.queryUserById(1);
       System.out.println(user2);
       sqlSession.close();
       
   	UserMapper mapper2 = sqlSession2.getMapper(UserMapper.class);
       User user = mapper2.queryUserById(1);
       System.out.println(user2);
       System.out.println(user1 == user2);
       sqlSession.close();
   }
   ```

   

小结：

* 只要开启了二级缓存，在同一个Mapper下就有效
* 所有的数据都会先放在一级缓存中
* 只有当会话提交，或者关闭的时候，才会提交到二级缓存中！



#### 13.5 缓存原理

![image-20210610220204873](https://cdn.jsdelivr.net/gh/BigHuang66/picBed@main/img/20210610220205.png)





#### 13.6、自定义缓存 - ehcatch



  













