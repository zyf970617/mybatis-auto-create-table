# mybatis-auto-create-table
这是`SpringBoot`整合`Mybatis`进行自动创建数据表的简单Demo（目前仅支持`mysql`）

框架开发自一位大佬，这里关键包也是引用这位大佬的，此处贴上传送门：https://github.com/sunchenbin/A.CTable-Frame

但是这个包的使用，原作者只提供了SpringMvc的使用demo，所以我整合了一份SpringBoot版本的mybatis自动创建表（全注解形式），使用是，需要添加`pom`文件中的关键包
```
<dependency>
  <groupId>com.gitee.sunchenbin.mybatis.actable</groupId>
  <artifactId>mybatis-enhance-actable</artifactId>
  <version>1.0.1</version>
</dependency>
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-lang3</artifactId>
  <version>3.4</version>
</dependency>
<dependency>
  <groupId>net.sf.json-lib</groupId>
  <artifactId>json-lib</artifactId>
  <version>2.4</version>
  <classifier>jdk15</classifier>
  <exclusions>
    <exclusion>
      <artifactId>commons-logging</artifactId>
      <groupId>commons-logging</groupId>
    </exclusion>
  </exclusions>
</dependency>
```
以上三个包，第一个是自动创建表的关键包，剩下的两个是第一个包中必须要用到的两个包，这三个都要导入，其他的类似于`Mysql`、`Mybatis`的包我这里就不累赘了

在使用方面要注意的事项就是，这里我放了两个配置文件，`application.yml`和`application.properties`，在`yml`文件中，放置的是一些基础的配置，包括所有项目的配置，在`properties`中，放置的是自动创建表的配置信息，如下
```
mybatis.table.auto=update
mybatis.model.pack=com.example.entity
mybatis.database.type=mysql
```

如果你想只用`yml`进行文件配置，那么你除了需要把这三个配置项移到`yml`文件外，还需要修改一个地方。
在`MybatisTableConfig.java`类中，通过注入`PropertiesFactoryBean`，将`properties`里的属性注入，如下
```
@Bean
public PropertiesFactoryBean configProperties() throws Exception{
    PropertiesFactoryBean propertiesFactoryBean = new PropertiesFactoryBean();
    PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
    propertiesFactoryBean.setLocations(resolver.getResources("classpath*:application.properties"));
    return propertiesFactoryBean;
}
```
如果你改成了只用`yml`，则需要修改如上配置文件，改成注入`yml`

最后，建议刚使用的时候，先克隆这个Demo跑一遍，里面代码不多，理解之后再整合到自己的项目中，对这个包的具体使用，可以参见原作者的文章http://blog.csdn.net/sun5769675/article/details/74779640 或者我扩展的文章 http://www.jianshu.com/p/25db002b0367 ，主要就是配置文件和实体层的使用（这个框架还提供了通用增删改查，因为我用了其他的框架来实现这个功能，所以就不展开了）


