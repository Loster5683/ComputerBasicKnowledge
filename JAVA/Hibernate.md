# **Hibernate**

### 为什么要用hibernate(jdbc的缺点)：

　　1、编程的时候很繁琐，用的try和catch比较多

　　2、jdbc没有做数据的缓存

　　3、没有做到面向对象编程

　　4、sql语句的跨平台性很差

### 五个核心接口：

- Configuration：负责配置并启动hibernate，创建SessionFactory　　

- SessionFactory：负责初始化hibernate，创建session对象

  - 1、hibernate中的配置文件、映射文件、持久化类的信息都在sessionFactory中
  - 2、sessionFactory中存放的信息都是共享的信息
  - 3、sessionFactory本身就是线程安全的
  - 4、一个hibernate框架sessionFactory只有一个
  - 5、sessionFactory是一个重量级别的类

- Session：负责被持久化对象CRUD操作

  - 1、得到了一个session，相当于打开了一次数据库的连接
  - 2、在hibernate中，对数据的crud操作都是由session来完成的

- Transaction：负责事物相关的操作

- - hibernate中的事务默认不是自动提交的

    　设置了connection的setAutoCommit为false

    ​    只有产生了连接，才能进行事务的操作。所以只有有了session以后，才能有transaction

- Query：负责执行各种数据库查询

[SSH之Hibernate总结篇](https://www.cnblogs.com/Java-web-wy/p/6533672.html)

### hibernate缓存机制

#### 一、N+1问题

**list()获得对象**

如果通过list()方法来获得对象,hibernate会发出一条sql语句，将所有的对象查询出来。

**iterator()获得对象**

当如果通过iterator()方法来获得我们对象的时候，hibernate首先会发出**1**条sql去查询出所有对象的 id 值，当我们如果需要查询到某个对象的具体信息的时候，hibernate此时会根据查询出来的 id 值再发sql语句去从数据库中查询对象的信息，这就是典型的 **N+1** 的问题。

**通过 list() 我们就不会出现 N+1的问题，那么iterator()为什么保留**如果我们需要在一个session当中要两次查询出很多对象，此时我们如果写两条 list()时，hibernate此时会发出两条 sql 语句，而且这两条语句是一样的，但是我们如果第一条语句使用 list()，而第二条语句使用 iterator()的话，此时我们也会发两条sql语句，但是第二条语句只会将查询出对象的id，所以相对应取出所有的对象而已，显然这样可以节省内存，而如果再要获取对象的时候，因为第一条语句已经将对象都查询出来了，此时会将对象保存到session的一级缓存中去，所以再次查询时，就会首先去缓存中查找，如果找到，则不发sql语句了。这里就牵涉到了接下来这个概念:hibernate的一级缓存。

#### 二、一级缓存(session级别)

##### Hibernate一级缓存API：

一级缓存是Hibernate的默认缓存，无法取消，常用方法：

1、evict():用于将某个对象从Session的一级缓存中清除。

2、clear():用于将一级缓存中的所有对象全部清除。

3、contains(obj)： 判断指定的对象是否存在于一级缓存中. 

4、flush() ：刷新一级缓存区的内容,使之与数据库数据保持同步. 

**hibernate的一级缓存是session级别的，默认开启，所以如果session关闭后，缓存就没了，此时就会再次发sql去查数据库**。

#### 三、二级缓存(sessionFactory级别)

二级缓存需要sessionFactory来管理，它是进程级的缓存，所有是session共享的，二级缓存比较复杂，一般用第

三方产品，官方推荐使用ehcache。

**二级缓存管理常用api**

1、session.setCacheMode(CacheMode.IGNORE):禁止使用二级缓存

2、evict(Class arg0, Serializable arg1): 将某个类的指定ID的持久化对象从二级缓存中清除,释放对象所占用的资源. 例如sessionFactory.evict(Student.class, new Integer(1));  

3、evict(Class arg0)  将指定类的所有持久化对象从二级缓存中清除,释放其占用的内存资源. 
例如sessionFactory.evict(Student.class);  

4、evictCollection(String arg0)  将指定类的所有持久化对象的指定集合从二级缓存中清除,释放其占用的内存资源. 
例如sessionFactory.evictCollection("Grade.students"); 

**二级缓存缓存的仅仅是对象，如果查询出来的是对象的一些属性，则不会被加到缓存中去**。

二级缓存是进程范围或者集群范围的缓存，有可能出现并发问题，因此需要采用适当的并发访问策略，该策略为被缓存的数据提供了事务隔离级别。

**什么样的数据适合存放到第二级缓存中？**
1 很少被修改的数据
2 不是很重要的数据，允许出现偶尔并发的数据
3 不会被并发访问的数据
4 常量数据
**不适合存放到第二级缓存的数据？**
1 经常被修改的数据
2 .绝对不允许出现并发访问的数据，如财务数据，绝对不允许出现并发
3 与其他应用共享的数据。

#### 四、查询缓存(sessionFactory级别)

​	一级缓存和二级缓存都是对象缓存:就是把该对象对应的数据库表中的所有的字段全部查询出来了，这种查询在某

些场合下会让效率降低。例如：表中的字段特别多，但是程序中所需要的字段却很少。

​       查询缓存也叫数据缓存：内存(页面)中需要多少数据就把多少数据放入到查询缓存中。

①查询缓存也是sessionFactory级别的缓存

②只有当 HQL 查询语句完全相同时，连参数设置都要相同，此时查询缓存才有效

③查询缓存也能引起 N+1 的问题,当我们将二级缓存注释掉以后，在使用查询缓存时，也会出现 N+1 的问题，为

什么呢？

​	因为**查询缓存缓存的也仅仅是对象的id**，所以第一条 sql 也是将对象的id都查询出来，但是当我们后面如果要

得到每个对象的信息的时候，此时又会发sql语句去查询，所以，如果要使用查询缓存，我们一定也要开启我们的

二级缓存，这样就不会出现 N+1 问题了

1.[hibernate缓存机制详细分析](https://www.cnblogs.com/xiaoluo501395377/p/3377604.html)

2.[Hibernate缓存简介和对比、一级缓存、二级缓存详解](https://blog.csdn.net/u012518860/article/details/78256874)