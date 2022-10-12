# MyBatis-Plus

## MyBatis-Plus快速入门

### MyBatis-Plus快速入门-简介

MyBatis-Plus（简称 MP）是一个 MyBatis的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

#### 特性

- 无侵入：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- 损耗小：启动即会自动注入基本 `CURD`，性能基本无损耗，直接面向对象操作
- 强大的 `CRUD `操作：内置通用 `Mapper`、通用 `Service`，仅仅通过少量配置即可实现单表大部分 `CRUD `操作，更有强大的条件构造器，满足各类使用需求
- 支持 `Lambda` 形式调用：通过 `Lambda `表达式，方便的编写各类查询条件，无需再担心字段写错
- 支持主键自动生成：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - `Sequence`），可自由配置，完美解决主键问题
- 支持 `ActiveRecord `模式：支持 `ActiveRecord `形式调用，实体类只需继承 `Model` 类即可进行强大的 `CRUD `操作
- 支持自定义全局通用操作：支持全局通用方法注入（ Write once, use anywhere ）
- 内置代码生成器：采用代码或者 `Maven `插件可快速生成 `Mapper` 、 `Model `、 `Service` 、 `Controller `层代码，支持模板引擎，更有超多自定义配置等您来使用
- 内置分页插件：基于 `MyBatis `物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 `List `查询
- 分页插件支持多种数据库：支持 `MySQL`、`MariaDB`、`Oracle`、`DB2`、`H2`、`HSQL`、`SQLite`、`Postgre`、`SQLServer `等多种数据库
- 内置性能分析插件：可输出 `SQL` 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- 内置全局拦截插件：提供全表 `delete `、 `update` 操作智能分析阻断，也可自定义拦截规则，预防误操作

#### 支持数据库

任何能使用 `MyBatis `进行 `CRUD`, 并且支持标准 `SQL `的数据库，具体支持情况如下：

- `MySQL`，`Oracle`，`DB2`，`H2`，`HSQL`，`SQLite`，`PostgreSQL`，`SQLServer`，`Phoenix`，`Gauss `，`ClickHouse`，`Sybase`，`OceanBase`，`Firebird`，`Cubrid`，`Goldilocks`，`csiidb`
- 达梦数据库，虚谷数据库，人大金仓数据库，南大通用(华库)数据库，南大通用数据库，神通数据库，瀚高数据库

#### 框架结构

<img src="D:\Typora笔记\MyBatis-Plus\image\快速入门-简介.png" style="zoom:70%;" />

### MyBatis-Plus 快速入门-快速开始

通过一个简单的 Demo 来阐述 MyBatis-Plus 的强大功能，在此之前，我们假设您已经：

- 拥有 `Java `开发环境以及相应 IDE
- 熟悉 `Spring Boot`
- 熟悉 `Maven`

现有一张 `User `表，其表结构如下：

| id   | name   | age  | email              |
| ---- | ------ | ---- | ------------------ |
| 1    | Jone   | 18   | test1@baomidou.com |
| 2    | Jack   | 20   | test2@baomidou.com |
| 3    | Tom    | 28   | test3@baomidou.com |
| 4    | Sandy  | 21   | test4@baomidou.com |
| 5    | Billie | 24   | test5@baomidou.com |

其对应的数据库 `Schema `脚本如下：

```sql
DROP TABLE IF EXISTS user;

CREATE TABLE user
(
    id BIGINT(20) NOT NULL COMMENT '主键ID',
    name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
    age INT(11) NULL DEFAULT NULL COMMENT '年龄',
    email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY (id)
);
```

其对应的数据库 `Data `脚本如下：

```sql
DELETE FROM user;

INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');
```

#### 初始化工程

创建一个空的 Spring Boot 工程（工程将以 H2 作为默认数据库进行演示）

提示：可以使用 [Spring Initializer](https://start.spring.io/)快速初始化一个 Spring Boot 工程

#### 添加依赖

引入 Spring Boot Starter 父工程：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.6.4</version>
    <relativePath/>
</parent>
```

引入 `spring-boot-starter`、`spring-boot-starter-test`、`mybatis-plus-boot-starter`、`h2 `依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.5.1</version>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

#### 配置

在 `application.yml` 配置文件中添加 `H2 `数据库的相关配置：

```yml
# DataSource Config
# db/XXXX-h2.sql文件夹放到resources文件下
spring:
  datasource:
    driver-class-name: org.h2.Driver
    schema: classpath:db/schema-h2.sql
    data: classpath:db/data-h2.sql
    url: jdbc:h2:mem:test
    username: root
    password: test
```

在 Spring Boot 启动类中添加 `@MapperScan` 注解，扫描 Mapper 文件夹：

```java
@SpringBootApplication
@MapperScan("com.renjiyue.mybatisplus.mapper")
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

#### 编码

编写实体类 `User.java`（此处使用了[Lombok](https://www.projectlombok.org/)简化代码）

```java
@Data
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

编写 Mapper 包下的 `UserMapper`接口

```java
@Mapper
public interface UserMapper extends BaseMapper<User> {
	@Override
    List<User> selectList(Wrapper<User> queryWrapper);
}
```

#### 开始使用

添加测试类，进行功能测试：

```java
@SpringBootTest
public class SampleTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    public void testSelect() {
        System.out.println(("----- selectAll method test ------"));
        List<User> userList = userMapper.selectList(null);
        userList.forEach(user -> {
			System.out.println(user.toString());
        });
    }
}
```

提示:`UserMapper`中的 `selectList()` 方法的参数为 `MP `内置的条件封装器 `Wrapper`，所以不填写就是无任何条件

控制台输出：

```java
User(id=1, name=Jone, age=18, email=test1@baomidou.com)
User(id=2, name=Jack, age=20, email=test2@baomidou.com)
User(id=3, name=Tom, age=28, email=test3@baomidou.com)
User(id=4, name=Sandy, age=21, email=test4@baomidou.com)
User(id=5, name=Billie, age=24, email=test5@baomidou.com)
```

#### 小结

通过以上几个简单的步骤，我们就实现了 `User `表的 `CRUD` 功能，甚至连 `XML `文件都不用编写！
从以上步骤中，我们可以看到集成​`MyBatis-Plus`​非常的简单，只需要引入 ​`starter `​工程，并配置 ​`mapper `​扫描路径即可。
但​` MyBatis-Plus`​ 的强大远不止这些功能，想要详细了解 ​`MyBatis-Plus`​ 的强大功能？那就继续往下看吧！

### MyBatis-Plus 快速入门-安装

2022-03-23 16:02 更新

全新的 `MyBatis-Plus` 3.0 版本基于 JDK8，提供了 `lambda `形式的调用，所以安装集成 MP3.0 要求如下：

- JDK 8+
- Maven or Gradle

#### Release

#### Spring Boot

Maven：

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.1</version>
</dependency>
```

Gradle：

```xml
compile group: 'com.baomidou', name: 'mybatis-plus-boot-starter', version: '3.5.1'
```

### Spring

Maven:

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus</artifactId>
    <version>3.5.1</version>
</dependency>
```

Gradle：

```java
compile group: 'com.baomidou', name: 'mybatis-plus', version: '3.5.1'
```

注意：引入 `MyBatis-Plus` 之后请不要再次引入 `MyBatis `以及 `MyBatis-Spring`，以避免因版本差异导致的问题。

#### Snapshot

快照 SNAPSHOT 版本需要添加仓库，且版本号为快照版本 [点击查看最新快照版本号](https://oss.sonatype.org/content/repositories/snapshots/com/baomidou/mybatis-plus-boot-starter/)。

Maven：

```xml
<repository>
    <id>snapshots</id>
    <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
</repository>
```

Gradle：

```xml
repositories {
    maven { url 'https://oss.sonatype.org/content/repositories/snapshots/' }
}
```

### MyBatis-Plus 快速入门-配置

`MyBatis-Plus` 的配置异常的简单，我们仅需要一些简单的配置即可使用 `MyBatis-Plus` 的强大功能！

#### Spring Boot 工程

- 配置 `MapperScan `注解

```java
@SpringBootApplication
@MapperScan("com.baomidou.mybatisplus.samples.quickstart.mapper")
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

#### Spring 工程

- 配置 `MapperScan`

```
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.baomidou.mybatisplus.samples.quickstart.mapper"/>
</bean>
```

- 调整 `SqlSessionFactory `为 `MyBatis-Plus` 的 `SqlSessionFactory`

```
<bean id="sqlSessionFactory" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

通常来说，一般的简单工程，通过以上配置即可正常使用 `MyBatis-Plus`

### MyBatis-Plus 快速入门-注解

本节将介绍 MybatisPlus 注解包相关类详解

#### @TableName

- 描述：表名注解，标识实体类对应的表
- 使用位置：实体类

```
@TableName("sys_user")
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

| 属性               | 类型     | 必须指定 | 默认值 | 描述                                                         |
| ------------------ | -------- | -------- | ------ | ------------------------------------------------------------ |
| `values`           | String   | 否       | ""     | 表名                                                         |
| `schema`           | String   | 否       | ""     | schema                                                       |
| `keepGlobalPrefix` | boolean  | 否       | false  | 是否保持使用全局的 tablePrefix 的值（当全局 tablePrefix 生效时） |
| `resultMap`        | String   | 否       | ""     | xml 中 resultMap 的 id（用于满足特定类型的实体类对象绑定）   |
| `autoResultMap`    | boolean  | 否       | false  | 是否自动构建 resultMap 并使用（如果设置 resultMap 则不会进行 resultMap 的自动构建与注入） |
| `excludeProperty`  | String[] | 否       | {}     | 需要排除的属性名                                             |

关于 `autoResultMap` 的说明：

MP 会自动构建一个 `resultMap`并注入到 MyBatis 里（一般用不上），请注意以下内容：

因为 MP 底层是 MyBatis，所以 MP 只是帮您注入了常用 CRUD 到 MyBatis 里，注入之前是动态的（根据您的 Entity 字段以及注解变化而变化），但是注入之后是静态的（等于 XML 配置中的内容）。

而对于 `typeHandler`属性，MyBatis 只支持写在 2 个地方:

1. 定义在 `resultMap `里，作用于查询结果的封装
2. 定义在 `insert `和 `update `语句的 `#{property}` 中的 `property `后面（例：`#{property,typehandler=xxx.xxx.xxx}`），并且只作用于当前 设置值

除了以上两种直接指定 `typeHandler `的形式，MyBatis 有一个全局扫描自定义 `typeHandler `包的配置，原理是根据您的 `property `类型去找其对应的 `typeHandler `并使用。

#### @TableId

- 描述：主键注解
- 使用位置：实体类主键字段

```
@TableName("sys_user")
public class User {
    @TableId
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

| 属性  | 类型   | 必须指定 | 默认值      | 描述         |
| ----- | ------ | -------- | ----------- | ------------ |
| value | String | 否       | ""          | 主键字段名   |
| type  | Enum   | 否       | IdType.NONE | 指定主键类型 |

#### IdType

| 值              | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| `AUTO`          | 数据库 ID 自增                                               |
| `NONE`          | 无状态，该类型为未设置主键类型（注解里等于跟随全局，全局里约等于 INPUT） |
| `INPUT`         | insert 前自行 set 主键值                                     |
| `ASSIGN_ID`     | 分配 ID(主键类型为 Number(Long 和 Integer)或 String)(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextId`(默认实现类为`DefaultIdentifierGenerator`雪花算法) |
| `ASSIGN_UUID`   | 分配 UUID,主键类型为 String(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextUUID`(默认 default 方法) |
| `ID_WORKER`     | 分布式全局唯一 ID 长整型类型(please use `ASSIGN_ID`)         |
| `UUID`          | 32 位 UUID 字符串(please use `ASSIGN_UUID`)                  |
| `ID_WORKER_STR` | 分布式全局唯一 ID 字符串类型(please use `ASSIGN_ID`)         |

#### @TableField

描述：字段注解（非主键）

```
@TableName("sys_user")
public class User {
    @TableId
    private Long id;
    @TableField("nickname")
    private String name;
    private Integer age;
    private String email;
}
```

| 属性               | 类型                        | 必须指定 | 默认值                   | 描述                                                         |
| ------------------ | --------------------------- | -------- | ------------------------ | ------------------------------------------------------------ |
| `value`            | String                      | 否       | ""                       | 数据库字段名                                                 |
| `exist`            | boolean                     | 否       | true                     | 是否为数据库表字段                                           |
| `ondition`         | String                      | 否       | ""                       | 字段`where`实体查询比较条件，有值设置则按设置的值为准，没有则为默认全局的`%s=#{%s}` |
| `update`           | String                      | 否       | ""                       | 字段 `update set` 部分注入，例如：当在version字段上注解`update="%s+1"` 表示更新时会 `set version=version+1` （该属性优先级高于 `el` 属性） |
| `insertStrategy`   | Enum                        | 否       | FieldStrategy.DEFAULT    | 举例：NOT_NULL `insert into table_a(<if test="columnProperty != null">column</if>) values (<if test="columnProperty != null">#{columnProperty}</if>)` |
| `updataStrategy`   | Enum                        | 否       | FieldStrategy.DEFAULT    | 举例：IGNORED `update table_a set column=#{columnProperty}`  |
| `whereStrategy`    | Enum                        | 否       | FieldStrategy.DEFAULT    | 举例：NOT_EMPTY `where <if test="columnProperty != null and columnProperty!=''">column=#{columnProperty}</if>` |
| `fill`             | Enum                        | 否       | FieldFill.DEFAULT        | 字段自动填充策略                                             |
| `select`           | boolean                     | 否       | true                     | 是否进行select查询                                           |
| `keepGlobalFormat` | boolean                     | 否       | false                    | 是否保持使用全局的format进行处理                             |
| `jabcType`         | jabcType                    | 否       | JdbcType.UNDEFINED       | JDBC类型（该默认值不代表会按照该值生效）                     |
| `typeHandler`      | Class<?extends TypeHandler> | 否       | UnknownTypeHandler.class | 类型处理器（该默认值不代表会按照该值生效）                   |
| `numericScale`     | String                      | 否       | ""                       | 指定小数点后保留的位数                                       |

关于`jdbcType`和`typeHandler`以及`numericScale`的说明:
​`numericScale`​只生效于 update 的 sql. ​`jdbcType`​和​`typeHandler`​如果不配合​`@TableName#autoResultMap = true`​一起使用,也只生效于 update 的 sql. 对于​`typeHandler`​如果你的字段类型和 set 进去的类型为​`equals`​关系,则只需要让你的​`typeHandler`​让 Mybatis 加载到即可,不需要使用注解

#### FieldStrategy

| 值          | 描述                                                        |
| ----------- | ----------------------------------------------------------- |
| `IGNORED`   | 忽略判断                                                    |
| `NOT_NULL`  | 非 NULL 判断                                                |
| `NOT_EMPTY` | 非空判断(只对字符串类型字段,其他类型字段依然为非 NULL 判断) |
| `DEFAULT`   | 追随全局配置                                                |

#### FieldFill

| 值              | 描述                 |
| --------------- | -------------------- |
| `DEFAULT`       | 默认不处理           |
| `INSERT`        | 插入时填充字段       |
| `UPDATE`        | 更新时填充字段       |
| `INSERT_UPDATE` | 插入和更新时填充字段 |

#### @Version

- 描述：乐观锁注解、标记 `@Verison` 在字段上

#### @EnumValue

- 描述：普通枚举类注解(注解在枚举字段上)

#### @TableLogic

- 描述：表字段逻辑处理注解（逻辑删除）

| 属性     | 类型   | 必须指定 | 默认值 | 描述         |
| -------- | ------ | -------- | ------ | ------------ |
| `value`  | String | 否       | ""     | 逻辑未删除值 |
| `delval` | String | 否       | ""     | 逻辑删除值   |

#### @KeySequence

- 描述：序列主键策略 `oracle`
- 属性：`value`、`resultMap`

| 属性    | 类型   | 必须指定 | 默认值     | 描述                                                         |
| ------- | ------ | -------- | ---------- | ------------------------------------------------------------ |
| `value` | String | 否       | ""         | 序列名                                                       |
| `clazz` | Class  | 否       | Long.class | id 的类型, 可以指定 String.class，这样返回的 Sequence 值是字符串"1" |

#### @OrderBy

- 描述：内置 SQL 默认指定排序，优先级低于 wrapper 条件查询

| 属性   | 类型    | 必须指定 | 默认值          | 描述           |
| ------ | ------- | -------- | --------------- | -------------- |
| isDesc | boolean | 否       | true            | 是否倒序查询   |
| sort   | short   | 否       | Short.MAX_VALUE | 数字越小越靠前 |

### MyBatis-Plus 快速入门-快速测试

自动导入 MyBatis-Plus 测试所需相关配置，通过 `@MybatisPlusTest` 注解快速配置测试类。

#### 添加测试依赖

Maven：

```
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter-test</artifactId>
    <version>3.5.1</version>
</dependency>
```

Gradle：

```
compile group: 'com.baomidou', name: 'mybatis-plus-boot-starter-test', version: '3.5.1'
```

#### 编写测试用例

通过 `@MybatisPlusTest` 可快速编写 Mapper 对应的测试类，实现快速测试代码

```
package com.renjiyue.mybatisplus;

import com.baomidou.mybatisplus.test.autoconfigure.MybatisPlusTest;
import com.renjiyue.mybatisplus.mapper.UserMapper;
import com.renjiyue.mybatisplus.pojo.User;
import org.apache.ibatis.javassist.tools.rmi.Sample;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.List;

@MybatisPlusTest
public class MybatisPlusSampleTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    void testInsert(){
        List<User> users = userMapper.selectList(null);
        users.forEach(user -> {
            System.out.println(user);
        });
    }
}
```

## MyBatis-Plus 核心功能-代码生成器（新）

注意：适用版本：mybatis-plus-generator 3.5.1 及其以上版本，对历史版本不兼容！

### 快速入门

#### 安装

```
		<!--mybatisplus依赖-->
 		<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>
        <!--代码生成模板-->
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.0</version>
        </dependency>
```

当前包未传递依赖 MP 包，需要自己引入！

#### 使用

##### 快速生成