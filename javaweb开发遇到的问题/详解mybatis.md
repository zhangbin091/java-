



### 一、创建maven工程，引入依赖包

##### 1、创建普通的maven工程



##### 2、引入mybatis相关依赖

```xml
		 <!--数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>
        <!--Mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
 		<dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.18</version>
            <scope>provided</scope>
        </dependency>
```

### 二、创建mybatis-config.xml配置文件

参考官网教程：

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>

</configuration>
```

注意：1、mysql5.0版本与mysql8.0版本驱动程序查别

​			2、url地址中存在&需要转义

### 三、创建pojo实体类、Dao层接口、mapper.xml文件

##### 1、创建pojo实体类

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
@Data
public class Books {
    private int bookId;
    private String bookName;
    private String detail;
    private int bookCounts;

}
```

注意：本工程引入了lombok包，通过注解可以创建构造函数、setter、getter方法。

##### 2、创建Dao接口

```java
import com.riant.pojo.Books;

import java.util.List;

public interface BookDao {
    List<Books> queryAllBook();

    int addBook(Books book);

    Books queryBookById(int id);

    int deletBookById(int id);

    int updateBook(Books book);

}

```

##### 3、创建mapper.xml映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.riant.dao.BookDao">
    <select id="queryAllBook" resultType="com.riant.pojo.Books">
    select * from ssmbuild.books;
  </select>

    <select id="queryBookById" parameterType="int" resultType="com.riant.pojo.Books">
        select * from ssmbuild.books where bookID=#{id}
    </select>
    <insert id="addBook" parameterType="com.riant.pojo.Books">
        insert into ssmbuild.books (bookName, bookCounts, detail) VALUES(#{bookName},#{bookCounts},#{detail})
    </insert>
    <delete id="deletBookById" parameterType="int">
        delete from ssmbuild.books where bookID=#{id}
    </delete>

    <update id="updateBook" parameterType="com.riant.pojo.Books">
        update ssmbuild.books set bookName=#{bookName},bookCounts=#{bookCounts},detail=#{detail} where bookID=#{bookId}
    </update>
</mapper>
```

注意：1、namespace命名空间必须携程Dao接口的全限定类名

​			2、当sql语句返回是对象或者list<对象>时，需要将resultType设成对象的全限定类名或别名

​			3、SQL语句中的参数是对象时，parameterType可以写成对象的全限定类名或别名，parameterType可以不写， MyBatis 可以通过类型处理器（TypeHandler）推断出具体传入语句的参数，SQL语句中的参数可以用#{字段名}替换

​			4、mapper.xml文件代替了原来Dao接口实现类的功能。

​			5、每条sql语句的ID都要与Dao层接口名一致

##### 4、在mybatis-config.xml引入mapper.xml文件

```xml
 	<mappers>
        <mapper resource="com/riant/dao/BookMapper.xml"/>
    </mappers>
```

有多少mapper.xml文件就引入多少，不然当调用对应Dao接口方法的时候mybatis为报错，

```
org.apache.ibatis.binding.BindingException: Type interface com.riant.dao.BookDao is not known to the MapperRegistry.
```

### 四、测试Dao层接口

##### 1、创建工具类获取SqlSession对象

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisTools {
    private static SqlSessionFactory sqlSessionFactory;

    static {
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    }

    public static SqlSession getSession() {
        return sqlSessionFactory.openSession();
    }
}

```

##### 2、创建测试文件

```
package com.riant.dao;
import com.riant.pojo.Books;
import com.riant.utils.MybatisTools;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class BookDaoTest {

    @Test
    public void test(){
        SqlSession sqlSession= MybatisTools.getSession();
        BookDao bookDao=sqlSession.getMapper(BookDao.class);
        List<Books> list=bookDao.queryAllBook();
        for(Books book : list){
            System.out.println(book.toString());
        }
    }

    @Test
    public void test2(){
        SqlSession sqlSession= MybatisTools.getSession();
        BookDao bookDao=sqlSession.getMapper(BookDao.class);
        Books books = new Books();
        books.setBookName("JVM入门");
        books.setBookCounts(10);
        books.setDetail("深入了解JVM");
        int returnValue=bookDao.addBook(books);
        System.out.println(returnValue);
        sqlSession.commit();
    }
    @Test
    public  void test3(){
        SqlSession sqlSession= MybatisTools.getSession();
        BookDao bookDao=sqlSession.getMapper(BookDao.class);
        Books book=bookDao.queryBookById(4);
        System.out.println(book.toString());
    }
    @Test
    public  void test4(){
        SqlSession sqlSession= MybatisTools.getSession();
        BookDao bookDao=sqlSession.getMapper(BookDao.class);
        int  returnValue=bookDao.deletBookById(9);
        System.out.println(returnValue);
        sqlSession.commit();
    }
    @Test
    public void test5(){
        SqlSession sqlSession= MybatisTools.getSession();
        BookDao bookDao=sqlSession.getMapper(BookDao.class);
        Books books = new Books();
        books.setBookId(2);
        books.setBookName("JVM入门");
        books.setBookCounts(10);
        books.setDetail("深入了解JVM");
        int returnValue=bookDao.updateBook(books);
        System.out.println(returnValue);
        sqlSession.commit();
        sqlSession.close();
    }
}

```



### 五、mybatis工作原理

每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。

##### 1、从xml中创建SqlSessionFactory 

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

SqlSessionFactoryBuilder().build()方法会解析xml文件，得到一个Configuration对象，该对象是通过xml文件中的<Configuration></Configuration>解析得到，将Configuration传给另外一个build()方法，得到一个DefaultSqlSessionFactory对象并返回，对类实现了SqlSessionFactory接口。

##### 2、不使用 XML 构建 SqlSessionFactory

```java
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
TransactionFactory transactionFactory = new JdbcTransactionFactory();
Environment environment = new Environment("development", transactionFactory, dataSource);
Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
```

##### 3、从 SqlSessionFactory 中获取 SqlSession

```java
SqlSession session = sqlSessionFactory.openSession()
```

SqlSessionFactory 功能就是创建连接器，它有多个接口，可以设置执行器类型ExecutorType、是否自动提交autoCommit、事务隔离级别TransactionIsolationLevel

ExecutorType分为三种：

1）SimpleExecutor

每次都会关闭statement。

```
try {
      Configuration configuration = ms.getConfiguration();
      StatementHandler handler = configuration.newStatementHandler(this, ms, parameter, RowBounds.DEFAULT, null, null);
      stmt = prepareStatement(handler, ms.getStatementLog());
      return handler.update(stmt);
    } finally {
      closeStatement(stmt);
    }
```

2）ReuseExecutor

ReuseExecutor维护了一个map,key为sql语句，value为statement对象，获取statement会先从map中查找是否存在SQL对应的statement，没有或者statement已经关闭则创建一个新的statement并放入map中。

```java
if (hasStatementFor(sql)) {
      stmt = getStatement(sql);
      applyTransactionTimeout(stmt);
    } else {
      Connection connection = getConnection(statementLog);
      stmt = handler.prepare(connection, transaction.getTimeout());
      putStatement(sql, stmt);
    }
```

不会关闭statement，而是把statement放到缓存中。

3）BatchExecutor







##### 4、执行sql语句

```java
BookDao bookDao=sqlSession.getMapper(BookDao.class);
Books book=bookDao.queryBookById(4);
```

上面语句实际上使用 了动态大理，代理类就是bookDao,

```
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    return mapperRegistry.getMapper(type, sqlSession);
  }
```

mapperRegistry这个类主要是用来注册并解析mapper,该对象是在在创建SQLSessionFactory过程中通过创建Configuration对象时生成的，是Configuration的一个成员，Configuration通过<mapper></mapper>,中配置的resources的xml地址，去加载文件，并根据namespace的值（Dao层接口全限定类名），将映射器代理工厂类添加到mapperRegistry中。

```java
public void parse() {
  if (!configuration.isResourceLoaded(resource)) {
    configurationElement(parser.evalNode("/mapper"));
    configuration.addLoadedResource(resource);
    bindMapperForNamespace();
  }

  parsePendingResultMaps();
  parsePendingCacheRefs();
  parsePendingStatements();
}
private void bindMapperForNamespace() {
    String namespace = builderAssistant.getCurrentNamespace();
    if (namespace != null) {
      Class<?> boundType = null;
      try {
        boundType = Resources.classForName(namespace);
      } catch (ClassNotFoundException e) {
        //ignore, bound type is not required
      }
      if (boundType != null) {
        if (!configuration.hasMapper(boundType)) {
          // Spring may not know the real resource name so we set a flag
          // to prevent loading again this resource from the mapper interface
          // look at MapperAnnotationBuilder#loadXmlResource
          configuration.addLoadedResource("namespace:" + namespace);
          configuration.addMapper(boundType);
        }
      }
    }
  }
public <T> void addMapper(Class<T> type) {
    mapperRegistry.addMapper(type);
  }

public <T> void addMapper(Class<T> type) {
    if (type.isInterface()) {
      if (hasMapper(type)) {
        throw new BindingException("Type " + type + " is already known to the MapperRegistry.");
      }
      boolean loadCompleted = false;
      try {
        knownMappers.put(type, new MapperProxyFactory<>(type));
        // It's important that the type is added before the parser is run
        // otherwise the binding may automatically be attempted by the
        // mapper parser. If the type is already known, it won't try.
        MapperAnnotationBuilder parser = new MapperAnnotationBuilder(config, type);
        parser.parse();
        loadCompleted = true;
      } finally {
        if (!loadCompleted) {
          knownMappers.remove(type);
        }
      }
    }
  }
```







```
public void addMappers(String packageName, Class<?> superType) {
    ResolverUtil<Class<?>> resolverUtil = new ResolverUtil<>();
    resolverUtil.find(new ResolverUtil.IsA(superType), packageName);
    Set<Class<? extends Class<?>>> mapperSet = resolverUtil.getClasses();
    for (Class<?> mapperClass : mapperSet) {
      addMapper(mapperClass);
    }
  }
```

通过xml中的配置，将

```
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) knownMappers.get(type);
    if (mapperProxyFactory == null) {
      throw new BindingException("Type " + type + " is not known to the MapperRegistry.");
    }
    try {
      return mapperProxyFactory.newInstance(sqlSession);
    } catch (Exception e) {
      throw new BindingException("Error getting mapper instance. Cause: " + e, e);
    }
  }
  
  protected T newInstance(MapperProxy<T> mapperProxy) {
    return (T) Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class[] { mapperInterface }, mapperProxy);
  }
```

通过mapperRegistry的获取mapper方法，就会从HashMap中获取对应的MapperProxyFactory<T>,然后newProxyInstance()方法会创建代理映射器。

MapperProxyFactory是一个代理工厂类，该类能创建代理类，该代理类会调用invoke()方法

```java
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    try {
      if (Object.class.equals(method.getDeclaringClass())) {
        return method.invoke(this, args);
      } else if (method.isDefault()) {
        return invokeDefaultMethod(proxy, method, args);
      }
    } catch (Throwable t) {
      throw ExceptionUtil.unwrapThrowable(t);
    }
    final MapperMethod mapperMethod = cachedMapperMethod(method);
    return mapperMethod.execute(sqlSession, args);
  }

  private MapperMethod cachedMapperMethod(Method method) {
    return methodCache.computeIfAbsent(method, k -> new MapperMethod(mapperInterface, method, sqlSession.getConfiguration()));
  }
```

通过mapperMethod.execute(sqlSession, args)执行方法，mapperMethod中会调用sqlSession的相关方法，然后选择对应的Executor（三种），通过configuration对象的newStatementHandler方法构建了一个StatementHandler，然后在调用prepareStatement方法中获取连接对象，通过StatementHandler得到Statement对象。另外我们注意到在获取了Statement对象后调用了parameterize方法。继续跟踪下去（自行跟踪哈）我们可以发现会调用到ParameterHandler对象的setParameters去处理我们的参数。所以这里的prepareStatement方法主要使用了StatementHandler和ParameterHandler对象帮助我们处理语句集和参数的处理。最后还调用了StatementHandler的query方法





