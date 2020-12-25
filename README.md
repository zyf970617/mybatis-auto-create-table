# mybatis-auto-create-table
这是`SpringBoot`整合`Mybatis`进行自动创建数据表的简单Demo（目前仅支持`mysql`）

框架开发自一位大佬，这里关键包也是引用这位大佬的，此处贴上传送门：https://gitee.com/sunchenbin/mybatis-enhance/

但是这个包的使用，原作者只提供了SpringMvc的使用demo，所以我整合了一份SpringBoot版本的mybatis自动创建表（全注解形式），使用时，需要添加`pom`文件中的关键包
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

###







