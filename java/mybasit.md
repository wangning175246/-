#### 介绍

实例了不用写dao类的实现类，通过代理和提供sql的方式实现对数据库的操作。

## 配置

#### mybasit 主配置文件

定义的是数据库的基础账户密码，驱动，连接池的配置，

mappers 中定义的是mapper xml文件的路径

- package 定义的是 mapper xml文件所在目录，通过这个方式引用mapper 文件中就不用写resultType的时候不用写全类名，只用写类的名称就可以，且类的名称可以不区分大小写
  - name 的值是相对于resources目录的路径，需要路径的中`\` 换成`.`

#### mapper xml配置文件的定义

mapper 类中定义是执行的sql语句和查询，

- resultMap 定义数据库表和java实体类的对象关系
  - 属性type表示对应的实体类的类名
  - 属性id 
  - id 定义的是主键
  - result 定义的是其他字段
    - property 定义是对应java实体的属性，
    - column 定义是查询结果中对应的数据库的字段的名称。
  - collection 表示对应实体类中的一个集合对象，
    - 属性 property  表示对应实体类中集合的名称，
    - 属性ofType 对应的是集合中属性的值。
  - association 表示对应实体类中的一个对象属性
    - 属性 property  表示对应实体类 对象属性的名称，
    - column 查询的时候根据那个数据库字段进行关联
    - javaType对象的java 数据类型。
- select update ...
  - 属性id
  - 属性 resultMap 表示引用上面定的resultMap类型
  - resultType  可以是java类型(int,Integer,String .....)，也可以是定义的对象类型。

## 连接池

#### JNDI

​	这个只能在tomcat中使用

## OGNL表达式：

Object Graphic Navigation Language
	对象	图	导航	   语言

它是通过对象的取值方法来获取数据。在写法上把get给省略了。
比如：我们获取用户的名称
	类中的写法：user.getUsername();
	OGNL表达式写法：user.username
mybatis中为什么能直接写username,而不用user.呢：
	因为在parameterType中已经提供了属性所属的类，所以此时不需要写对象名

## 查询

#### CURD

#### 一对多

#### 多对一

#### 多对多查询

## 延迟加载

多表关联查询的时候，查询主表数据的时候不会把被关联表的数据查询出来，而是在使用被关联表的数据的时候才去查询。主要是用的时候调用对方配置文件中的查询语句进行查询。

1. 需要在主配置中开启延迟加载
2. 需要修改

## 缓存

#### 一级缓存

#### 二级缓存

