# mybatis-auto-create-table
这是`SpringBoot`整合`Mybatis`进行自动创建数据表的简单Demo（目前仅支持`mysql`）

这个包的使用，原作者只提供了SpringMvc的使用demo，所以我整合了一份SpringBoot版本的mybatis自动创建表（全注解形式），使用时，需要添加`pom`文件中的关键包
```xml
<dependency>
  <groupId>com.gitee.sunchenbin.mybatis.actable</groupId>
  <artifactId>mybatis-enhance-actable</artifactId>
  <version>1.4.1.RELEASE</version>
</dependency>
```
关于其他用到的包就不累赘了，比如`Mybatis`、`MySQL`等等。刚开始跑的时候，建议克隆这个Demo跑一遍，之后再整合到自己的项目中。

### 配置信息
```properties
# 可以设置为create（纯创建）、add（添加新的不删除内容）、update（更新内容，推荐用）、none（啥都不干）
actable.table.auto=update  
# 用于创建数据表的待扫描目录，该目录下的类如果有@Table注解，就会进行扫描
actable.model.pack=com.example.entity
# 创建类型，目前仅支持mysql
actable.database.type=mysql
```
以上配置信息是写在`application.properties`里的，如果你的项目不使用`application.properties`而使用`application.yml`，没关系，一样的写法，如下
```yml
actable:
  table:
    auto: update
  model:
    pack: com.example.entity
  database:
    type: mysql
```

> 如果你用过旧版，旧版使用的是`mybatis.table.auto`、`mybatis.model.pack`和`mybatis.database.type`，也可以继续用，内部进行了兼容


### 创建表用法一

- 使用`@Table`和`@Column`方式定义，需要的属性配置都定义在`@Column`中，相对比较繁琐，如下所示
```java
@Table(name = "test")
public class Test extends BaseModel {

	@Column(name = "id",type = MySqlTypeConstant.INT,length = 11,isKey = true,isAutoIncrement = true)
	private Integer	id;

	@Column(name = "name",type = MySqlTypeConstant.VARCHAR, length = 45, defaultValue = "hello")
	private String	name;

	@Column(name = "type",type = MySqlTypeConstant.VARCHAR,length = 45)
	private String	type;

	@Column(name = "description",type = MySqlTypeConstant.TEXT)
	private String	description;

	@Column(name = "create_time",type = MySqlTypeConstant.DATETIME)
  
  //...
}
```
### 创建表用法二（推荐）
- 使用`@Table`和`@Column`方式定义，但是可以使用其他的注解，例如`@Id`，`@IsNotNull`，`@isAutoIncrement`等等，同时，类型，名称不用定义也没有关系。如果类型没有定义，就自动将Java的类型转换过去，如果字段名没有定义，就自动使用驼峰规则将变量名转换为字段名。
- 使用这种方法记得将需要转换的字段都标上`@Column`注解，不然不会被转换，如下所示
```java
@Table(name = "test1")
public class Test1 extends BaseModel {

    @Id
    @IsAutoIncrement
    @Column
    private Integer	id;

    @Column
    private Double price;

    @Column
    @ColumnType(value = MySqlTypeConstant.VARCHAR, length = 455)
    private String name;

    @Column
    private Date time;
    
    //...
}
```
### 创建用法三（懒人用法）
- 这种方式只需要添加一个注解，`@Table(isSimple = true)`，当然想添加额外的注解也是可以的。在只添加这一个注解的情况下，该类里的所有字段都会被当做加上了`@Column`，并且会按照类型转换、名称转换等为其建立对应的类型、名称、长度等等。表名也是，可以自己设置，也可以自动按照驼峰规则转换
- 这种方法虽然相对轻松，但是有个弊端就是会转换所有的字段，假设继承了一个`BaseModel`，里面有`page`、`orderBy`等信息，那么就不建议使用这种方式，如下所示
```java
@Table(isSimple = true)
public class UserLogin {

	@IsKey
	@IsAutoIncrement
	private Integer	id;

	private String	name;

	private String	type;
  
  //...

}
```

### 其他
1. 该框架有一个`BaseCRUD`，可以直接用这个也可以用其他的`mybatis-plus`之类的框架
2. 目前支持使用的MySQL类型如下
```java
DEFAULT(null,null,null),
INT(1, 11, null),
VARCHAR(1, 255, null),
BINARY(1, 1, null),
CHAR(1, 255, null),
BIGINT(1, 20, null),
BIT(1, 1, null),
TINYINT(1, 4, null),
SMALLINT(1, 6, null),
MEDIUMINT(1, 9, null),
DECIMAL(2, 10, 2),
DOUBLE(2, 10, 2),
TEXT(0, null, null),
MEDIUMTEXT(0, null, null),
LONGTEXT(0, null, null),
DATETIME(0, null, null),
TIMESTAMP(0, null, null),
DATE(0, null, null),
TIME(0, null, null),
FLOAT(2, 10, 2),
YEAR(0, null, null),
BLOB(0, null, null),
LONGBLOB(0, null, null),
MEDIUMBLOB(0, null, null),
TINYTEXT(0, null, null),
TINYBLOB(0, null, null),
JSON(0, null, null);
```
3. Java类型自动转换为MySQL类型的转换方案如下（X,Y），即为X转换为Y
```java
("class java.lang.String", MySqlTypeConstant.VARCHAR)
("class java.lang.Long", MySqlTypeConstant.BIGINT)
("class java.lang.Integer", MySqlTypeConstant.INT)
("class java.lang.Boolean", MySqlTypeConstant.BIT)
("class java.math.BigInteger", MySqlTypeConstant.BIGINT)
("class java.lang.Float", MySqlTypeConstant.FLOAT)
("class java.lang.Double", MySqlTypeConstant.DOUBLE)
("class java.math.BigDecimal", MySqlTypeConstant.DECIMAL)
("class java.sql.Date", MySqlTypeConstant.DATE)
("class java.util.Date", MySqlTypeConstant.DATE)
("class java.sql.Timestamp", MySqlTypeConstant.DATETIME)
("class java.sql.Time", MySqlTypeConstant.TIME)
("class java.time.LocalDateTime", MySqlTypeConstant.DATETIME)
("class java.time.LocalDate", MySqlTypeConstant.DATE)
("class java.time.LocalTime", MySqlTypeConstant.TIME)
("long", MySqlTypeConstant.BIGINT)
("int", MySqlTypeConstant.INT)
("boolean", MySqlTypeConstant.BIT)
("float", MySqlTypeConstant.FLOAT)
("double", MySqlTypeConstant.DOUBLE)
("byte", MySqlTypeConstant.TINYINT)
("short", MySqlTypeConstant.SMALLINT)
("char", MySqlTypeConstant.VARCHAR)
```
4. 支持表引擎、字符集、表注释等等，详见`MySqlCharsetConstant`和`MySqlEngineConstant`
5. 自己扩展其实也比较方便，后面有时间出一版怎么自己扩展的教程~

> 框架开发自一位大佬，这里关键包也是引用这位大佬的，此处贴上传送门：https://gitee.com/sunchenbin/mybatis-enhance/ 。其他细节信息有需要可以看原作者的文档，感谢开源~






