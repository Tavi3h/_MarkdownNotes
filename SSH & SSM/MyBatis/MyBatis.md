[TOC]

# MyBatis

## 第一章 MyBatis入门

![MyBatis](images\MyBatis.png)

### 1.1 MyBatis概述

#### 1.1.1 MyBatis简介

MyBatis本是apache的一个开源项目iBatis，2010年这个项目由apache迁移到了google，并更名为MyBatis，2013年迁移到Github。

MyBatis是一个有优秀的基于Java的持久层框架，它内部封装了JDBC，使开发者只需关注SQL语句本身，而不用再花费精力去处理诸如注册驱动、创建Connection、配置statement等复杂过程。

MyBatis通过xml或注解的方式将要执行的各种statement配置起来，并通过Java对象和Statement中SQL的动态参数进行映射生成最终执行的SQL语句，最后由MyBatis框架执行SQL并将结果映射成Java对象并返回。

#### 1.1.2 MyBatis与Hibernate

Hibernate框架是提供了全面的数据库封装机制的“全自动”ORM，即实现了POJO和数据库之间的映射，以及SQL的自动生成和执行。
相对于此，MyBatis只能算是“半自动”ORM。其着力点是在POJO类与SQL语句之间的映射关系。也就是说，MyBatis并不会为程序员自动生成SQL语句。具体的SQL语句需要程序员自己编写，然后欧通国SQL语句映射文件将SQL所需的参数以及返回的结果字段映射到指定的POJO 。因此MyBatis成为了“全自动”ORM的一种有益补充。

与Hibernate相比，MyBatis具有以下几个特点：

- 在XML中配置SQL语句，实现了SQL语句与代码的分离，提高程序的可维护性。
- 因为SQL语句需要自己编写，程序员可以结合数据库自身的特点灵活控制SQL语句，因此能够实现比Hibernate等全自动ORM框架更高的查询效率。
- 简单、易用、上手快。

#### 1.1.3 MyBatis使用Jar包

*使用版本3.4.5。*

MyBatis框架的加压目录中只有一个Jar包，它是MyBatis的核心Jar包。另外还有个lib目录，其中存放着MyBatis所依赖的Jar包。所以使用MyBatis需要将其核心Jar包及lib下的所有Jar包导入。

- 核心：mybatis-3.4.5.jar
- lib：
    + ant-1.9.6.jar
    + ant-launcher-1.9.6.jar
    + asm-5.2.jar
    + cglib-3.2.5.jar
    + commons-logging-1.2.jar
    + javassist-3.22.0-CR2.jar
    + log4j-1.2.17.jar
    + log4j-api-2.3.jar
    + log4j-core-2.3.jar
    + ognl-3.1.15.jar
    + slf4j-api-1.7.25.jar
    + slf4j-log4j12-1.7.25.jar

#### 1.1.4 MyBatis体系结构及原理

体系结构：

![structure](images\structure.png)

原理示意图：

![principle](images\principle.png) 

- mybatis.xml：主配置文件
- mapper.xml：映射文件

### 1.2 第一个MyBatis程序

#### 1.2.1 基本程序

*源代码在MyBatis-01-primary-01下。*

**（1）导入Jar包**：

除了MyBatis使用的Jar包外，再添加JUnit和MySQL驱动的Jar包。

**（2）定义实体类**：

    package tavish.bit.beans;

    public class Student {
        private Integer id;
        private String name;
        private int age;
        private double score;

        public Student() {
            super();
        }

        public Student(String name, int age, double score) {
            super();
            this.name = name;
            this.age = age;
            this.score = score;
        }

        public Integer getId() {
            return id;
        }

        public void setId(Integer id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public double getScore() {
            return score;
        }

        public void setScore(double score) {
            this.score = score;
        }

        @Override
        public String toString() {
            return "Student [id=" + id + ", name=" + name + ", age=" + age + ", score=" + score + "]";
        }
    }

**（3）在MySQL中创建表**：

    CREATE TABLE `student` (
      `id` int(5) NOT NULL AUTO_INCREMENT,
      `name` varchar(20) DEFAULT NULL,
      `age` int(3) DEFAULT NULL,
      `score` double DEFAULT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8

*由于后面要创建MyBatis映射文件映射的是SQL语句，而非Hibernate是类到表，属性到字段的映射。所以MyBatis要求在创建数据库表时，字段名要与属性名一致（当然不一致时，可以通过resultMap解决）。*

**（4）定义Dao接口**：

    package tavish.bit.dao;

    import tavish.bit.beans.Student;

    public interface IStudentDao {
        void insertStu(Student student);
    }

**（5）定义映射文件**：mapper.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="test">
        <!-- 为sql语句命名 -->
        <insert id="insertStudent" parameterType="tavish.bit.beans.Student">
            <!-- 传入参数必须与属性同名 -->
            insert into student(name,age,score) values(#{name},#{age},#{score})
        </insert>
    </mapper>

**（6）定义主配置文件**：mybatis.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <!-- 配置运行环境 -->
        <environments default="mySQLEnv">
            <environment id="mySQLEnv">
                <!-- 配置事务管理 -->
                <transactionManager type="JDBC" />
                <!-- 配置数据源，使用MyBatis默认的数据源 -->
                <dataSource type="POOLED">
                    <!-- 配置数据库链接四要素 -->
                    <property name="driver" value="com.mysql.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                    <property name="username" value="root"/>
                    <property name="password" value="mysql"/>
                </dataSource>
            </environment>
        </environments>
        
        <!-- 注册映射文件 -->
        <mappers>
            <mapper resource="tavish/bit/dao/mapper.xml" />
        </mappers>
    </configuration>

**（7）定义Dao实现类**：

    package tavish.bit.dao;

    import java.io.IOException;
    import java.io.InputStream;

    import org.apache.ibatis.io.Resources;
    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.apache.ibatis.session.SqlSessionFactoryBuilder;

    import tavish.bit.beans.Student;

    public class StudentDaoImpl implements IStudentDao {
        
        private SqlSession sqlSession;

        @Override
        public void insertStu(Student student) {
            try {
                // 加载主配置文件
                InputStream inputStream = Resources.getResourceAsStream("mybatis.xml");
                // 创建SqlSessionFactory对象
                SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
                sqlSession = sqlSessionFactory.openSession();
                // 执行相关操作
                sqlSession.insert("insertStudent", student);
                // 提交事务
                sqlSession.commit();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (sqlSession != null) {
                    sqlSession.close();
                }
            }
        }
    }

**如果不提交事务，系统也会执行sql语句，但不会更新数据库，因为发生了回滚。**

**（8）配置log4j**：

    ### 输出信息到控制台 ###
    log4j.appender.stdout = org.apache.log4j.ConsoleAppender
    log4j.appender.stdout.Target = System.out
    log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
    log4j.appender.stdout.layout.ConversionPattern = [%-5p][%d{yyyy-MM-dd HH:mm:ss}] %c %L %m%n

    log4j.logger.test = debug,stdout

**（9）定义测试类**：

    package tavish.bit.test;

    import org.junit.Before;
    import org.junit.Test;

    import tavish.bit.beans.Student;
    import tavish.bit.dao.IStudentDao;
    import tavish.bit.dao.StudentDaoImpl;

    public class MyTest {

        private IStudentDao dao;

        @Before
        public void testBefore() {
            dao = new StudentDaoImpl();
        }

        @Test
        public void testInsert() {
            Student student = new Student("赵六", 26, 93.6);
            dao.insertStu(student);
        }
    }


执行测试方法，数据插入成功。

#### 1.2.2 映射文件中namespace的作用

*源代码在MyBatis-01-primary-2下。*

namespace指的是mapper标签下的namespace属性值，该属性值指明要执行语句的命名空间。

示例：

复制mapper.xml，命名为mapper2.xml并在主配置文件中注册：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="test">
        <!-- 为sql语句命名 -->
        <insert id="insertStudent" parameterType="tavish.bit.beans.Student">
            <!-- 传入参数必须与属性同名 -->
            insert into student(name,age,score) values(#{name},#{age},#{score})
        </insert>
    </mapper>

注册：

    <mappers>
        <mapper resource="tavish/bit/dao/mapper.xml" />
        <mapper resource="tavish/bit/dao/mapper2.xml" />
    </mappers>

此时执行testInsert测试方法会发生报错：

    Caused by: java.lang.IllegalArgumentException: Mapped Statements collection already contains value for test.insertStudent

这是因为此时Dao实现类的insertStu方法执行的是id为“insertStudent”语句，而现在有两个相同的命名空间（test）都包含有该id的语句，导致程序不知道应该执行哪一个。

修改mapper2.xml中的namespace，将值修改为test2，再次执行测试方法依然发生报错：

    Caused by: java.lang.IllegalArgumentException: insertStudent is ambiguous in Mapped Statements collection (try using the full name including the namespace, or rename one of the entries)

这是因为虽然此时两个映射文件中的namespace不冲突，但是insertStu中的方法却没有指明究竟应该执行哪一个命名空间下id为“insertStudent”语句，即不同命名空间下的语句发生了id冲突。

解决方案有两种：

**（1）**：修改Dao实现类，在实现类中指明要使用哪个命名空间下的语句。

    sqlSession.insert("test.insertStudent", student);

此时执行的是命名空间为test中id为insertStudent的sql语句。

**（2）**：修改id属性，使不同命名空间下的语句具有不同的id。

    <mapper namespace="test2">
        <!-- 为sql语句命名 -->
        <insert id="insertStudent2" parameterType="tavish.bit.beans.Student">
            <!-- 传入参数必须与属性同名 -->
            insert into student(name,age,score) values(#{name},#{age},#{score})
        </insert>
    </mapper>

此时直接使用id即可：

    sqlSession.insert("insertStudent2", student);

此时执行的是id为insertStudent2的sql语句。

#### 1.2.3 定义工具类

定义：

    package tavish.bit.utils;

    import java.io.IOException;
    import org.apache.ibatis.io.Resources;
    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.apache.ibatis.session.SqlSessionFactoryBuilder;

    /*
     * SqlSessionFactory对象是重量级对象，并且是线程安全的。
     */

    public class MyBatisUtils {
        
        private static SqlSessionFactory sqlSessionFactory;
        
        public static SqlSession getSqlSession() {
            if (sqlSessionFactory == null) {
                try {
                    sqlSessionFactory = new SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("mybatis.xml"));
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            return sqlSessionFactory.openSession();
        }
    }

使用：

    package tavish.bit.dao;

    import org.apache.ibatis.session.SqlSession;
    import tavish.bit.beans.Student;
    import tavish.bit.utils.MyBatisUtils;

    public class StudentDaoImpl implements IStudentDao {
        
        private SqlSession sqlSession;

        @Override
        public void insertStu(Student student) {
            try {
                // 获取SqlSession
                sqlSession = MyBatisUtils.getSqlSession();
                // 执行语句
                sqlSession.insert("insertStudent", student);
                // 提交事务
                sqlSession.commit();
            } finally {
                if (sqlSession != null) {
                    sqlSession.close();
                }
            }
        }
    }

测试成功。

#### 1.2.4 从属性文件中读取DB链接四要素

*源代码在MyBatis-01-primary-3下。*

在classpath下创建jdbc.mysql.properties文件：

    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/test
    jdbc.user=root
    jdbc.password=mysql

同时在配置文件中加载属性文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>

        <!-- 注册属性文件 -->
        <properties resource="jdbc.mysql.properties" />

        <!-- 配置运行环境 -->
        <environments default="mySQLEnv">
            <environment id="mySQLEnv">
                <!-- 配置事物管理 -->
                <transactionManager type="JDBC" />
                <!-- 配置数据源，使用MyBatis默认的数据源 -->
                <dataSource type="POOLED">
                    <!-- 配置数据库链接四要素 -->
                    <property name="driver" value="${jdbc.driver}"/>
                    <property name="url" value="${jdbc.url}"/>
                    <property name="username" value="${jdbc.user}"/>
                    <property name="password" value="${jdbc.password}"/>
                </dataSource>
            </environment>
        </environments>
        
        <!-- 注册映射文件 -->
        <mappers>
            <mapper resource="tavish/bit/dao/mapper.xml" />
        </mappers>
    </configuration>

#### 1.2.5 设置类的别名

在主配置文件的properties标签和environments标签中间设置类别名标签typeAliases：

**第一种写法，使用typeAlias标签：**

    <typeAliases>
        <typeAlias type="tavish.bit.beans.Student" alias="Student"/>
    </typeAliases>

映射文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="test">
        <!-- 为sql语句命名 -->
        <insert id="insertStudent" parameterType="Student">
            <!-- 传入参数必须与属性同名 -->
            insert into student(name,age,score) values(#{name},#{age},#{score})
        </insert>
    </mapper>

此时设置了类tavish.bit.beans.Student的别名为Student。

**第二种写法，使用pakcage标签：**

    <typeAliases>
        <package name="tavish.bit.beans"/>
    </typeAliases>

映射文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="test">
        <!-- 为sql语句命名 -->
        <insert id="insertStudent" parameterType="Student">
            <!-- 传入参数必须与属性同名 -->
            insert into student(name,age,score) values(#{name},#{age},#{score})
        </insert>
    </mapper>

此时默认tavish.bit.beans包下的类的别名为其简单类名。

#### 1.2.6 内置类的别名

除了自定义的类型的别名外，MyBatis还提供了内置的类型别名：

基本类型：

|类型|别名|类型|别名|
-----|-----|-----|-----
int|_int|int|_integer
short|_short|long|_long
double|_double|float|_float
byte|_byte|boolean|_boolean

常用包装类型：

|类型|别名|类型|别名|
-----|-----|-----|-----
String|string|Byte|byte
Long|long|Short|short
Integer|int|Integer|integer
Double|double|Float|float
Boolean|boolean|Date|date
Object|object|Collection|collection
List|list|ArrayList|arraylist
Map|map|HashMap|hashmap
Iterator|iterator||

### 1.3 API

#### 1.3.1 概述

**（1）Resources类**：

Resources类用于读取资源文件。其具有很多方法通过加载并解析资源文件返回不同类型的IO流对象。

**（2）SqlSessionFactoryBuilder类**：

SqlSessionFactory的创建需要使用SqlSessionFactoryBuilder对象的build()方法。由于SqlSessionFactoryBuilder对象在创建完工厂对象后就完成了其历史使命，即可被销毁，所以一般会将该SqlSessionFactoryBuilder对象创建为一个方法内的局部对象，方法结束，对象销毁。

该类中有各种被重载的build()方法。

**（3）SqlSessionFactory接口**：

SqlSession接口对象用于执行持久化操作。一个SqlSession对应着一次数据库会话，一
次会话以SqlSession对象的创建开始，以SqlSession对象的关闭结束。

SqlSession 接口对象是线程不安全的，所以每次数据库会话结束前，需要马上调用其close()方法，将其关闭。再次需要会话，则再次创建。而在关闭时会判断当前的SqlSession是否被提交：**若没有被提交，则会执行回滚后关闭；若已被提交，则直接将SqlSession关闭。**

*所以，SqlSession 无需手工回滚。*

#### 1.3.2 相关源码分析

##### 1.3.2.1 InputStream的关闭

在输入流使用完毕后，不用手工进行流的关闭，因为在输入流被使用后，SqlSessionFactoryBuilder对象的build()方法会自动将输入流关闭。

![inputStream_build_1](images\inputStream_build_1.png)

![inputStream_build_2](images\inputStream_build_2.png)

##### 1.3.2.2 SqlSession的创建

SqlSession对象的创建，需要使用SqlSessionFactory接口对象的openSession()方法。

SqlSessionFactory 接口的实现类为DefaultSqlSessionFactory。

![sqlSession_1](images\sqlSession_1.png)

openSession()方法的源码：

![sqlSession_2](images\sqlSession_2.png)

![sqlSession_3](images\sqlSession_3.png)

![sqlSession_4](images\sqlSession_4.png)

从以上源码可以看到，无参的openSession()方法，将事务的自动提交直接赋值为false。而所谓创建SqlSession，就是加载了主配置文件，创建了一个执行器对象（将来用于执行映射文件中的SQL 语句），初始化了一个DB数据被修改的标志变量dirty，并闭了事务的自动提交功能。

##### 1.3.2.3 增删改的执行

对于SqlSession的insert()、delete()、update()方法，其底层均是调用执行了update()方法。

![idu_1](images\idu_1.png)

![idu_2](images\idu_2.png)

![idu_3](images\idu_3.png)

**从以上源码可知，无论执行增、删还是改，均是对数据进行修改，均将dirty变量设置为true，且在获取到映射文件中指定id的SQL语句后，由执行器executor执行。**

##### 1.3.2.4 SqlSession的提交----commit()

对sqlSession.commit()方法进行跟踪：

选择其实现类：**DefaultSqlSession。**

![sqlSession_commit_1](images\sqlSession_commit_1.png)

![sqlSession_commit_2](images\sqlSession_commit_2.png)

![sqlSession_commit_3](images\sqlSession_commit_3.png)

由于autoCommit为false，dirty为true（在执行commit()方法前执行了insert()方法，insert()方法的执行将导致dirty为true），所以表达式(!autoCommit && true)为true，此时发生短路，导致isCommitOrRollbackRequired(false)返回true。

![sqlSession_commit_4](images\sqlSession_commit_4.png)

即接下来将执行executor.commit(true);

继续向下查看源码：选择其实现类BaseExecutor

![sqlSession_commit_5](images\sqlSession_commit_5.png)

![sqlSession_commit_6](images\sqlSession_commit_6.PNG)

此时required为true，则执行事务提交。

**至此我们发现，SqlSession在执行了insert()等方法后，执行commit()方法会导致事务的提交。**另一方面，**如果没有执行过insert()等方法，则意味着dirty为false，此时执行commit()方法则不会提交事务。**

在执行了executor.commit()方法后，dirty被重新置为false，表明数据库和内存数据此时一致。


##### 1.3.2.4 SqlSession的关闭----close()

sqlSession.close()方法有两种使用情形（执行了insert()等方法，此时dirty为true）：

- 在调用close()方法前调用了commit()方法。
- 在调用close()方法前没有调用commit()方法。


**（1）在调用close()方法前调用了commit()方法**：

由于调用commit()方法，由上一节可知，此时dirty被重新置为false。此时跟踪close()方法：

![sqlSession_close_1](images\sqlSession_close_1.PNG)

选择DefaultSqlSession。

![sqlSession_close_2](images\sqlSession_close_2.PNG)

此时执行的是isCommitOrRollbackRequired(false)方法。

![sqlSession_close_3](images\sqlSession_close_3.PNG)

由于dirty此时为false，导致这个方法最终返回false，即接下来执行executor.close(false);

选择实现类BaseExecutor：

![sqlSession_close_4](images\sqlSession_close_4.PNG)

此时forceRollback的值为false，所以执行rollback(false)：

![sqlSession_close_5](images\sqlSession_close_5.PNG)

因为required的值为false，所以没有发生回滚：

![sqlSession_close_6](images\sqlSession_close_6.PNG)

在执行完rollback(false)后，由于transaction不为null，所以事务关闭。

**
即，在调用close()方法前调用了commit()方法，在这种情况下，调用close()方法不会发生事务回滚，但会关闭事务。
**

**（2）在调用close()方法前没有调用commit()方法**：

如果在调用close()前没有调用commit()方法，则此时dirty为true，即isCommitOrRollbackRequired(false)方法会返回true。则接下来将执行executor.close(true)方法。

也即执行rollback(true)方法，所以发生回滚。回滚后事务关闭，并将dirty置为false。

## 第二章 单表的CURD操作

CURD操作，是指对数据库中实体对象的增create、改update、查Read、删Delete操作。

### 2.1 定义Dao接口

    package tavish.bit.dao;

    import java.util.List;
    import java.util.Map;

    import tavish.bit.beans.Student;

    public interface IStudentDao {
        // 插入
        void insertStudent(Student student);
        
        // 插入后当前对象自动获得id
        void insertStudentCacheId(Student student);
        
        // 根据id删除
        void deleteStudentById(int id);
        
        // 修改
        void updateStudentById(Student student);

        // 查询所有---List
        List<Student> selectAllStudents();
        
        // 查询所有---Map
        Map<String, Object> selectAllStudentsMap();
        
        // 根据id查询
        Student selectStudentById(int id);
        
        // 根据名字模糊查询
        List<Student> selectStudentsByName(String name);
    }

### 2.2 具体实现

#### 2.2.1 插入 - - - - void insertStudent(Student student)

代码：

    @Override
    public void insertStudent(Student student) {
        try {
            // 获取SqlSession
            sqlSession = MyBatisUtils.getSqlSession();
            // 执行语句
            sqlSession.insert("insertStudent", student);
            // 提交事务
            sqlSession.commit();
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
    }

SQL：

    <!-- 插入Student -->
    <insert id="insertStudent" parameterType="Student">
        <!-- 传入参数必须与属性同名 -->
        insert into student(name,age,score) values(#{name},#{age},#{score})
    </insert>

测试：

    @Test
    public void testInsertStudent() {
        Student student = new Student("赵二六", 26, 93.6);
        System.out.println("插入前：student = " + student);
        dao.insertStudent(student);
        System.out.println("插入后：student = " + student);
    }

#### 2.2.2 插入 - - - - void insertStudentCacheId(Student student)

代码：

    @Override
    public void insertStudentCacheId(Student student) {
        try {
            // 获取SqlSession
            sqlSession = MyBatisUtils.getSqlSession();
            // 执行语句
            sqlSession.insert("insertStudentCacheId", student);
            // 提交事务
            sqlSession.commit();
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
    }

SQL：

    <!-- 插入后立即使Student对象立即获得id -->
    <insert id="insertStudentCacheId" parameterType="Student">
        insert into student(name,age,score) values(#{name},#{age},#{score})
        <!-- resultType：查出的类型；keyProperty：查到的数据对应的实体类的属性；order：执行insert语句后再对id进行赋值 -->
        <selectKey resultType="int" keyProperty="id" order="AFTER">
            select @@identity
        </selectKey>
    </insert>

与上一节的insert不同，该操作在插入数据后，被插入对象会立即获得id属性。

测试：

    @Test
    public void testInsertStudentCacheId() {
        Student student = new Student("Triela", 27, 93.7);
        System.out.println("插入前：student = " + student);
        dao.insertStudentCacheId(student);
        System.out.println("插入后：student = " + student);
    }

输出：

    插入前：student = Student [id=null, name=Triela, age=27, score=93.7]
    [DEBUG][2018-01-19 21:06:35] test.insertStudentCacheId 159 ==>  Preparing: insert into student(name,age,score) values(?,?,?) 
    [DEBUG][2018-01-19 21:06:35] test.insertStudentCacheId 159 ==> Parameters: Triela(String), 27(Integer), 93.7(Double)
    [DEBUG][2018-01-19 21:06:35] test.insertStudentCacheId 159 <==    Updates: 1
    [DEBUG][2018-01-19 21:06:35] test.insertStudentCacheId!selectKey 159 ==>  Preparing: select @@identity 
    [DEBUG][2018-01-19 21:06:35] test.insertStudentCacheId!selectKey 159 ==> Parameters: 
    [DEBUG][2018-01-19 21:06:35] test.insertStudentCacheId!selectKey 159 <==      Total: 1
    插入后：student = Student [id=20, name=Triela, age=27, score=93.7]


#### 2.2.3 插入 - - - - void deleteStudentById(int id)

代码：

    @Override
    public void deleteStudentById(int id) {
        try {
            // 获取SqlSession
            sqlSession = MyBatisUtils.getSqlSession();
            // 执行语句
            sqlSession.delete("deleteStudentById", id);
            // 提交事务
            sqlSession.commit();
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
    }

SQL：

    <!-- 删除操作 -->
    <delete id="deleteStudentById">
        delete from student where id=#{xxx} <!-- 这里的#{}仅仅是占位符，可以放任意字符串 -->
    </delete>

测试：

    @Test
    public void testDeleteStudentById() {
        dao.deleteStudentById(20);
    }

#### 2.2.4 修改 - - - - void updateStudentById(Student student)

代码：

    @Override
    public void updateStudentById(Student student) {
        try {
            // 获取SqlSession
            sqlSession = MyBatisUtils.getSqlSession();
            // 执行语句
            sqlSession.update("updateStudentById", student);
            // 提交事务
            sqlSession.commit();
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
    }

SQL：

    <!-- 修改操作 -->
    <update id="updateStudentById">
        update student set name=#{name}, age=#{age}, score=#{score} where id=#{id}
    </update>

测试：

    @Test
    public void testUpdateStudentById() {
        Student student = new Student("Triela", 24, 94.4);
        student.setId(18);
        dao.updateStudentById(student);
    }

#### 2.2.4 查询 - - - - List<Student> selectAllStudents()

代码：

    @Override
    public List<Student> selectAllStudents() {
        
        List<Student> students = null;
        try {
            // 获取SqlSession
            sqlSession = MyBatisUtils.getSqlSession();
            // 执行语句
            students = sqlSession.selectList("selectAllStudents");
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
        return students;
    }

SQL：

    <!-- 查询所有学生 -->
    <!-- 指明resultType以便系统对查询结果进行封装 -->
    <select id="selectAllStudents" resultType="Student">
        select id, name, age, score from student
    </select>

测试：

    // 测试查询---查询所有学生返回List
    @Test
    public void testSelectAllStudents() {
        List<Student> students = dao.selectAllStudents();
        for (Student student : students) {
            System.out.println(student);
        }
    }

#### 2.2.5 查询 - - - - Map<String, Object> selectAllStudentsMap()

代码：

    @Override
    public Map<String, Object> selectAllStudentsMap() {
        Map<String, Object> studentsMap = null;
        try {
            // 获取SqlSession
            sqlSession = MyBatisUtils.getSqlSession();
            // 执行语句
            // selectMap方法的第二个参数为mapKey，即以Student对象的哪一个属性值作为Map的key
            studentsMap = sqlSession.selectMap("selectAllStudentsMap", "name");
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
        return studentsMap;
    }

SQL：

    <!-- 指明resultType以便系统对查询结果进行封装，返回Map -->
    <select id="selectAllStudentsMap" resultType="Student">
        select id, name, age, score from student
    </select>

测试：

    @Test
    public void testSelectAllStudentsMap() {
        Map<String, Object> studentsMap = dao.selectAllStudentsMap();
        // 以name属性的值作为Map的key来获取其对应的value
        System.out.println(studentsMap.get("Triela"));
        // 当key相同时，获取到的value是最后查出的哪个对象
        for (String str : studentsMap.keySet()) {
            System.out.println(studentsMap.get(str));
        }
    }

#### 2.2.6 查询 - - - - Student selectStudentById(int id)

代码：

    @Override
    public Student selectStudentById(int id) {
        
        Student student = null;
        try {
            // 获取SqlSession
            sqlSession = MyBatisUtils.getSqlSession();
            // 执行语句
            student = sqlSession.selectOne("selectStudentById", id);
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
        return student;
    }

SQL：

    <!-- 根据id查询一个学生 --> 
    <select id="selectStudentById" resultType="Student">
        select id, name, age, score from student where id=#{xxx}
    </select>

测试：

    @Test
    public void testSelectStudentById() {
        Student student = dao.selectStudentById(8);
        System.out.println(student);
    }

#### 2.2.7 查询 - - - - List<Student> selectStudentsByName(String name)

代码：

    @Override
    public List<Student> selectStudentsByName(String name) {
        List<Student> students = null;
        try {
            // 获取SqlSession
            sqlSession = MyBatisUtils.getSqlSession();
            // 执行语句
            students = sqlSession.selectList("selectStudentsByName", name);
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }
        return students;
    }

SQL：

    <!-- 根据名字模糊查询 -->
    <select id="selectStudentsByName" resultType="Student">
        <!-- concat函数用于字符串连接 -->
        <!-- select id, name, age, score from student where name like concat('%', #{xxx}, '%') -->
        <!-- 底层使用纯粹的字符串拼接即statement，{}内必须使用value -->
        <!-- select id, name, age, score from student where name like '%${value}%' -->
        <!-- 底层使用preparedStatement -->
        select id, name, age, score from student where name like '%' #{xxx} '%'
    </select>

测试：

    @Test
    public void testSelectStudentsByName() {
        List<Student> students = dao.selectStudentsByName("T");
        for (Student student : students) {
            System.out.println(student);
        }
    }

### 2.3 查询时字段名与属性名不一致

resultType可以将查询结果直接映射为实体Bean对象的条件是：SQL查询的字段名与实体Bean的属性名一致。因为在将查询结果转换为指定类型对象时，系统自动将查询结果字段名称作为对象的属性名，通过反射机制完成对象的创建。

当SQL查询结果的字段名和实体Bean的属性名不一致时，将无法创建出对应的对象。此时有两种解决方案。

- 查询字段使用别名
- 使用结果映射resultMap

*字段名与属性名不一致只影响查询结果的封装，对增删改没有影响。*

#### 2.3.1 搭建测试环境

*源代码在MyBatis-03-property-column_inconsistency下。*

**（1）修改student表：**

将id、name、age分别修改为tid、tname、tage：

    CREATE TABLE `student` (
      `tid` int(5) NOT NULL AUTO_INCREMENT,
      `tname` varchar(20) DEFAULT NULL,
      `tage` int(3) DEFAULT NULL,
      `score` double DEFAULT NULL,
      PRIMARY KEY (`tid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=22 DEFAULT CHARSET=utf8

**（2）修改Dao：**

接口：

    package tavish.bit.dao;

    import java.util.List;
    import tavish.bit.beans.Student;

    public interface IStudentDao {

        // 查询所有---List
        List<Student> selectAllStudents();
        
        // 根据id查询
        Student selectStudentById(int id);
    }

实现类：

    package tavish.bit.dao;

    import java.util.List;
    import org.apache.ibatis.session.SqlSession;
    import tavish.bit.beans.Student;
    import tavish.bit.utils.MyBatisUtils;

    public class StudentDaoImpl implements IStudentDao {
        
        private SqlSession sqlSession;

        // 查询所有学生，返回List
        @Override
        public List<Student> selectAllStudents() {
            
            List<Student> students = null;
            try {
                // 获取SqlSession
                sqlSession = MyBatisUtils.getSqlSession();
                // 执行语句
                students = sqlSession.selectList("selectAllStudents");
            } finally {
                if (sqlSession != null) {
                    sqlSession.close();
                }
            }
            return students;
        }

        // 根据id查询一个学生
        @Override
        public Student selectStudentById(int id) {
            
            Student student = null;
            try {
                // 获取SqlSession
                sqlSession = MyBatisUtils.getSqlSession();
                // 执行语句
                student = sqlSession.selectOne("selectStudentById", id);
            } finally {
                if (sqlSession != null) {
                    sqlSession.close();
                }
            }
            return student;
        }
    }

配置文件：

    <select id="selectAllStudents" resultType="Student">
        select id, name, age, score from student
    </select>
    <select id="selectStudentById" resultType="Student">
        select id, name, age, score from student where id=#{xxx}
    </select>

**（3）测试：**

*在不进行任何修改的前提下进行测试：*执行selectAllStudents方法

发生报错：

    org.apache.ibatis.exceptions.PersistenceException: ### Error querying database. Cause: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Unknown column 'id' in 'field list' 

由于此时表中字段名已是txxx，所以修改SQL语句：

    <select id="selectAllStudents" resultType="Student">
        select tid, tname, tage, score from student
    </select>

再次测试：

未发生报错，程序通过测试，但查询结果为：

    Student [id=null, name=null, age=0, score=93.5]
    Student [id=null, name=null, age=0, score=94.5]
    Student [id=null, name=null, age=0, score=95.5]
    Student [id=null, name=null, age=0, score=93.6]
    Student [id=null, name=null, age=0, score=100.0]
    Student [id=null, name=null, age=0, score=93.6]
    Student [id=null, name=null, age=0, score=99.4]

此时只查出了score，而其他属性均为null。这是因为查询到的tid、tname、tage信息无法通过反射赋值给Student对象，因为此时的属性名是id、name、age。

#### 2.3.2 查询字段使用别名

解决方案一，使用别名。修改SQL：

    <select id="selectAllStudents" resultType="Student">
        select tid id, tname name, tage age, score from student
    </select>

将各个字段的别名设置为与类属性名相同的名字。

再次执行测试，输出结果：

    Student [id=1, name=张三, age=23, score=93.5]
    Student [id=2, name=李四, age=24, score=94.5]
    Student [id=3, name=王五, age=25, score=95.5]
    Student [id=7, name=赵六, age=26, score=93.6]
    Student [id=8, name=Tavish, age=23, score=100.0]
    Student [id=15, name=赵二六, age=26, score=93.6]
    Student [id=16, name=Tavish, age=24, score=99.4]


#### 2.3.3 通过resultMap解决

在mapper.xml中设置resultMap：

    <!-- 设置resultMap -->
    <resultMap type="Student" id="studentMapper">
        <id column="tid" property="id"/>
        <result column="tname" property="name"/>
        <result column="tage" property="age"/>
    </resultMap>

引用resultMap：

    <!-- 使用resultMap解决不一致问题 -->
    <select id="selectStudentById" resultMap="studentMapper">
        select tid, tname, tage, score from student where tid=#{xxx}
    </select>

测试输出：

    Student [id=8, name=Tavish, age=23, score=100.0]



**
完整的mapper.xml：
**

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="test">
        <!-- 设置resultMap -->
        <resultMap type="Student" id="studentMapper">
            <id column="tid" property="id"/>
            <result column="tname" property="name"/>
            <result column="tage" property="age"/>
        </resultMap>
        
        <!-- 查询所有学生 -->
        <!-- 使用别名来解决不一致问题 -->
        <select id="selectAllStudents" resultType="Student">
            select tid id, tname name, tage age, score from student
        </select>
        
        <!-- 根据id查询一个学生 --> 
        <!-- 使用resultMap解决不一致问题 -->
        <select id="selectStudentById" resultMap="studentMapper">
            select tid, tname, tage, score from student where tid=#{xxx}
        </select>
    </mapper>


### 2.4 Mapper动态代理

在前面例子中自定义Dao接口实现类时发现一个问题：Dao的实现类骑士并没有干什么实质性的工作，它仅仅是通过SqlSession的相关API定位到映射文件mapper中相应id的SQL语句，真正对DB进行操作的工作其实是由框架通过mapper中SQL完成的。

所以，MyBatis框架就抛开了Dao实现类，直接定位到映射文件mapper中相应SQL语句，对DB进行操作。这种对Dao的实现方式称为Mapper的动态代理方式。

Mapper动态代理方式无需程序员实现Dao接口。接口是由MyBatis结合映射文件自动生成的动态代理实现的。

**
使用Mapper动态代理的前提是Dao接口中的方法名与映射文件中sql语句的id相同。
**

实现：

*源代码在MyBatis-04-MapperDynamicProxy下。*

**（1）修改mapper.xml中的namespace：**

一般情况下，一个Dao接口的实现类方法使用的是同一个SQL映射文件中的SQL映射id。所以MyBatis框架要求，将映射文件中&lt;mapper/&gt;标签的namespace属性设为Dao接口的全限定性类名，则系统会根据方法所属Dao接口，自动到相应namespace的映射文件中查找相关的SQL映射。

    <mapper namespace="tavish.bit.dao.IStudentDao">
        ...
    </mapper>

**（2）修改日志输出控制文件：**

由于mapper的namespace属性被修改了，所以需要重新设置日志输出：

    ### 输出信息到控制台 ###
    log4j.appender.stdout = org.apache.log4j.ConsoleAppender
    log4j.appender.stdout.Target = System.out
    log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
    log4j.appender.stdout.layout.ConversionPattern = [%-5p][%d{yyyy-MM-dd HH:mm:ss}] %c %L %m%n

    log4j.logger.tavish.bit.dao.IStudentDao = debug,stdout

**（3）修改Dao接口方法名：**

**
使用Mapper动态代理的前提是Dao接口中的方法名与映射文件中SQL语句的id相同。
**

**（4）Dao对象的获取：**

使用时，只需要调用SqlSession的getMapper()方法，即可获取指定接口的实现类对象。该方法的参数为指定Dao接口类的class。

*这里获取到的dao是由JDK的Proxy实现的。*

    public class MyTest {

        private IStudentDao dao;
        private SqlSession sqlSession;

        @Before
        public void testBefore() {
            sqlSession = MyBatisUtils.getSqlSession();
            dao = sqlSession.getMapper(IStudentDao.class);
        }

        // ...
    }

由于Dao的实现工作完全由MyBatis系统自动根据映射文件完成。所以不再需要Dao实现类，将Dao实现类删除。

**（5）修改测试类：**

由于没有Dao的实现类，所以我们需要在@After注解方法中关闭SqlSession对象：

    @After
    public void testAfter() {
        if (sqlSession != null) {
            sqlSession.close();
        }
    }

同时，要在增删改的测试方法中加入sqlSession的提交。

    @Test
    public void testInsertStudent() {
        Student student = new Student("赵三六", 26, 93.6);
        System.out.println("插入前：student = " + student);
        dao.insertStudent(student);
        System.out.println("插入后：student = " + student);
        sqlSession.commit();
    }

**（6）删除testSelectAllStudentsMap()方法：**

**MyBatis框架对于Dao查询的自动实现，底层只会调用selectOne()与selectList()方法。**而框架选择方法的标准是测试类中用于接收返回值的对象类型。若接收类型为List，则自动选择与selectList()方法；否则自动选择selectOne()方法。

由于该方法的返回值类型为Map，所以框架选择了与selectOne()方法，会发生报错：

    org.apache.ibatis.exceptions.TooManyResultsException: Expected one result (or null) to be returned by selectOne(), but found: 9

### 2.5 多查询条件问题

在实际工作中，表单中所给出的查询条件有时是无法将其封装为一个对象的，也就是说，查询方法只能携带多个参数，而不能携带将这多个参数进行封装的一个对象。对于这个问题，有两种解决方案：

- 将多个参数封装为一个Map
- 使用索引号逐个接收

#### 2.5.1 将多个参数封装为一个Map

*源代码在MyBatis-05-selectByMap下。*

将多个参数封装为一个Map<String, Object>，根据Map进行查询。

需求：查询名字中带有“T”，年龄大于23且成绩大于某个学生成绩的学生

该需求所用的参数无法封装为单个对象，此时可以将参数封装为Map

**（1）定义Dao接口：**

    List<Student> selectStudentsByCondition(Map<String, Object> paramsMap);

**（2）定义测试方法：**

    @Test
    public void testSelectStudentsByMap() {
        
        // 定义一个临时的学生
        Student stu = new Student("Taco", 27, 95);
        
        // 查询条件：名字中带有“T”，年龄大于23且成绩大于Taco成绩
        Map<String, Object> paramsMap = new HashMap<>();
        // 封装参数nameCondition
        paramsMap.put("nameCondition", "T");
        // 封装参数ageCondition
        paramsMap.put("ageCondition", 23);
        // 将stu作为参数进行封装
        paramsMap.put("stu", stu);
        
        List<Student> students = dao.selectStudentsByCondition(paramsMap);
        for (Student student : students) {
            System.out.println(student);
        }
    }

**（3）SQL语句：**

    <!-- 根据条件查询 -->
    <!-- SQL语句中的#{}内部可以写对象属性、占位符以及Map的key -->
    <select id="selectStudentsByCondition" resultType="Student">
        select id, name, age, score 
        from student 
        where name like '%' #{nameCondition} '%' 
        and age > #{ageCondition}
        and score > #{stu.score}
    </select>

测试结果：

    Student [id=16, name=Tavish, age=24, score=99.4]

#### 2.5.2 使用索引号逐个接收

*源代码在MyBatis-05-selectByParamsIndex下。*

对于mapper中的SQL语句，可以通过参数索引#{index}的方式逐个接收参数。

需求同上。

**（1）定义Dao接口：**

    List<Student> selectStudentsByCondition(String nameCon, int ageCon, double scoreCon);

**（2）定义测试方法：**

    @Test
    public void testSelectStudentsByParamsIndex() {

        // 定义一个临时的学生
        Student stu = new Student("Taco", 27, 95);
        
        String nameCondition = "T";
        int ageCondition = 23;
        
        List<Student> students = dao.selectStudentsByCondition(nameCondition, ageCondition, stu.getScore());
        for (Student student : students) {
            System.out.println(student);
        }
    }

**（3）SQL语句：**

    <!-- 根据条件查询 -->
    <!-- SQL语句中的#{}内部可以写对象属性、占位符、Map的key以及参数索引 -->
    <select id="selectStudentsByCondition" resultType="Student">
        select id, name, age, score 
        from student 
        where name like '%' #{param1} '%' 
        and age > #{param2}
        and score > #{param3}
    </select>

**
这里使用参数索引为param1、param2、...，也可以使用arg0、arg1、...
**

测试结果：

    Student [id=16, name=Tavish, age=24, score=99.4]

### 2.6 动态SQL

*源代码在MyBatis-06-dynamicSQL下。*

动态SQL，主要用于解决查询条件不确定的情况：在程序运行期间，根据用户提交的查询条件进行查询。提交的查询条件不同，执行SQL语句不同。若将每种可能的情况均逐一列出，对所有条件进行排列组合则会出现大量的SQL语句。此时，可使用动态SQL来解决这样的问题。

复杂的表单：

![form](images\form.png)

动态SQL，即通过MyBatis提供的各种标签对条件做出判断以实现动态拼接SQL语句。

这里的条件判断使用的表达式为OGNL表达式。

注意：

在mapper的动态SQL中若出现大于号（>）、小于号（<）等符号最好将其转为实体符号。否则，XML可能会出现解析错误。

|-|小于|小于等于|大于|大于等于|and|单引号|双引号|
-----|-----|-----|-----|-----|-----|-----|-----
原符号|<|<=|>|>=|&|'|"
替换符号|& lt;|& lt;=|& gt;|& gt;=|& amp;|& apos;|& quot;

实现功能：查询出满足用户提交查询条件的所有学生。

用户提交的查询条件可以包含一个姓名的模糊查询，同时还可以包含一个年龄下限。当然用户提交表单时可能两个条件均做出了设定，也可能两个条件均不做出设定，也可能只做其中一个设定。

这引发的问题是查询条件不确定，查询条件依赖于用户提交的内容。此时就可使用动态SQL语句，根据用户提交内容对将要执行的SQL语句进行拼接。

#### 2.6.1 &lt;if/&gt;标签

对于该标签的执行，当test值为true时，会将其包含的SQL片段拼接到其所在的SQL语句中。

**（1）定义Dao接口：**

    public interface IStudentDao {
        List<Student> selectStudentsByIf(Student student);
    }

**（2）定义测试类：**

    @Test
    public void testSelectStudentsByIf() {
        // 由于查询不涉及score属性，所以统一设置为0
        int score = 0;
        // 无条件查询
        Student stu1 = new Student("", 0, score);
        
        // 全条件查询
        Student stu2 = new Student("T", 23, score);
        
        // 只查询姓名
        Student stu3 = new Student("T", 0, score);

        // 只查询年龄
        Student stu4 = new Student("", 23, score);
        
        List<Student> students1 = dao.selectStudentsByIf(stu1);
        for (Student student : students1) {
            System.out.println(student);
        }
        List<Student> students2 = dao.selectStudentsByIf(stu2);
        for (Student student : students2) {
            System.out.println(student);
        }
        List<Student> students3 = dao.selectStudentsByIf(stu3);
        for (Student student : students3) {
            System.out.println(student);
        }
        List<Student> students4 = dao.selectStudentsByIf(stu4);
        for (Student student : students4) {
            System.out.println(student);
        }
    }

**（3）SQL语句：**

    <!-- 根据条件查询，使用if标签 -->
    <select id="selectStudentsByIf" resultType="Student">
        select id, name, age, score 
        from student 
        where 1 = 1
        <if test="name != null and name != ''">
            and name like '%' #{name} '%'
        </if>
        <if test="age > 0">
            and age > #{age}
        </if>
    </select>

测试输出：此时SQL语句通过条件动态生成。

    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 ==>  Preparing: select id, name, age, score from student where 1 = 1 
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 ==> Parameters: 
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 <==      Total: 9
    Student [id=1, name=张三, age=23, score=93.5]
    Student [id=2, name=李四, age=24, score=94.5]
    Student [id=3, name=王五, age=25, score=95.5]
    Student [id=7, name=赵六, age=26, score=93.6]
    Student [id=8, name=Tavish, age=23, score=100.0]
    Student [id=15, name=赵二六, age=26, score=93.6]
    Student [id=16, name=Tavish, age=24, score=99.4]
    Student [id=24, name=赵三六, age=26, score=93.6]
    Student [id=25, name=Triela, age=27, score=93.7]
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 ==>  Preparing: select id, name, age, score from student where 1 = 1 and name like '%' ? '%' and age > ? 
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 ==> Parameters: T(String), 23(Integer)
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 <==      Total: 2
    Student [id=16, name=Tavish, age=24, score=99.4]
    Student [id=25, name=Triela, age=27, score=93.7]
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 ==>  Preparing: select id, name, age, score from student where 1 = 1 and name like '%' ? '%' 
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 ==> Parameters: T(String)
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 <==      Total: 3
    Student [id=8, name=Tavish, age=23, score=100.0]
    Student [id=16, name=Tavish, age=24, score=99.4]
    Student [id=25, name=Triela, age=27, score=93.7]
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 ==>  Preparing: select id, name, age, score from student where 1 = 1 and age > ? 
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 ==> Parameters: 23(Integer)
    [DEBUG][2018-01-24 21:58:08] tavish.bit.dao.IStudentDao.selectStudentsByIf 159 <==      Total: 7
    Student [id=2, name=李四, age=24, score=94.5]
    Student [id=3, name=王五, age=25, score=95.5]
    Student [id=7, name=赵六, age=26, score=93.6]
    Student [id=15, name=赵二六, age=26, score=93.6]
    Student [id=16, name=Tavish, age=24, score=99.4]
    Student [id=24, name=赵三六, age=26, score=93.6]
    Student [id=25, name=Triela, age=27, score=93.7]

**
虽然测试通过，但是在实际使用中where 1=1 会造成性能的浪费。
**

#### 2.6.2 &lt;where/&gt;标签

if标签中存在一个问题：需要在where中手工添加1=1子句。但当数据量很大时，会严重影响查询效率。

功能需求同上。

**（1）修改Dao接口：**

    public interface IStudentDao {
        List<Student> selectStudentsByIf(Student student);
        List<Student> selectStudentsByWhere(Student student);
    }

**(2）定义测试类：**

    @Test
    public void testSelectStudentsByWhere() {
        // 由于查询不涉及score属性，所以统一设置为0
        int score = 0;
        // 无条件查询
        Student stu1 = new Student("", 0, score);
        
        // 全条件查询
        Student stu2 = new Student("T", 23, score);
        
        // 只查询姓名
        Student stu3 = new Student("T", 0, score);
        
        // 只查询年龄
        Student stu4 = new Student("", 23, score);
        
        List<Student> students1 = dao.selectStudentsByWhere(stu1);
        for (Student student : students1) {
            System.out.println(student);
        }
        List<Student> students2 = dao.selectStudentsByWhere(stu2);
        for (Student student : students2) {
            System.out.println(student);
        }
        List<Student> students3 = dao.selectStudentsByWhere(stu3);
        for (Student student : students3) {
            System.out.println(student);
        }
        List<Student> students4 = dao.selectStudentsByWhere(stu4);
        for (Student student : students4) {
            System.out.println(student);
        }
    }

**（3）SQL语句：**

    <!-- 根据条件查询，使用where标签 -->
    <select id="selectStudentsByWhere" resultType="Student">
        select id, name, age, score 
        from student 
        <where>
            <if test="name != null and name != ''">
                and name like '%' #{name} '%'
            </if>
            <if test="age > 0">
                and age > #{age}
            </if>
        </where>
    </select>

测试输出：当没有查询条件时不会有WHERE子句生成；当有查询条件时，系统自动去掉了WHERE之后的and。

    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 ==>  Preparing: select id, name, age, score from student 
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 ==> Parameters: 
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 <==      Total: 9
    Student [id=1, name=张三, age=23, score=93.5]
    Student [id=2, name=李四, age=24, score=94.5]
    Student [id=3, name=王五, age=25, score=95.5]
    Student [id=7, name=赵六, age=26, score=93.6]
    Student [id=8, name=Tavish, age=23, score=100.0]
    Student [id=15, name=赵二六, age=26, score=93.6]
    Student [id=16, name=Tavish, age=24, score=99.4]
    Student [id=24, name=赵三六, age=26, score=93.6]
    Student [id=25, name=Triela, age=27, score=93.7]
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 ==>  Preparing: select id, name, age, score from student WHERE name like '%' ? '%' and age > ? 
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 ==> Parameters: T(String), 23(Integer)
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 <==      Total: 2
    Student [id=16, name=Tavish, age=24, score=99.4]
    Student [id=25, name=Triela, age=27, score=93.7]
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 ==>  Preparing: select id, name, age, score from student WHERE name like '%' ? '%' 
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 ==> Parameters: T(String)
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 <==      Total: 3
    Student [id=8, name=Tavish, age=23, score=100.0]
    Student [id=16, name=Tavish, age=24, score=99.4]
    Student [id=25, name=Triela, age=27, score=93.7]
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 ==>  Preparing: select id, name, age, score from student WHERE age > ? 
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 ==> Parameters: 23(Integer)
    [DEBUG][2018-01-24 22:09:14] tavish.bit.dao.IStudentDao.selectStudentsByWhere 159 <==      Total: 7
    Student [id=2, name=李四, age=24, score=94.5]
    Student [id=3, name=王五, age=25, score=95.5]
    Student [id=7, name=赵六, age=26, score=93.6]
    Student [id=15, name=赵二六, age=26, score=93.6]
    Student [id=16, name=Tavish, age=24, score=99.4]
    Student [id=24, name=赵三六, age=26, score=93.6]
    Student [id=25, name=Triela, age=27, score=93.7]

测试通过。

#### 2.6.3 &lt;choose/&gt;标签

该标签中只可以包含when和otherwise标签，他们共同完成Java中的开关语句switch功能。

需求：

若姓名不空，则按照姓名查询；若姓名为空，则按照年龄查询；若没有查询条件，则没有查询结果。

**（1）修改Dao接口：**

    public interface IStudentDao {
        List<Student> selectStudentsByIf(Student student);
        List<Student> selectStudentsByWhere(Student student);
        List<Student> selectStudentsByChoose(Student student);
    }

**（2）定义测试类：**全条件查询相当于只设置姓名的查询，故省略。

    @Test
    public void testSelectStudentsByChoose() {
        // 由于查询不涉及score属性，所以统一设置为0
        int score = 0;
        // 无条件查询
        Student stu1 = new Student("", 0, score);
        
        // 只查询姓名
        Student stu3 = new Student("T", 0, score);
        
        // 只查询年龄
        Student stu4 = new Student("", 23, score);
        
        List<Student> students1 = dao.selectStudentsByChoose(stu1);
        for (Student student : students1) {
            System.out.println(student);
        }
        List<Student> students3 = dao.selectStudentsByChoose(stu3);
        for (Student student : students3) {
            System.out.println(student);
        }
        List<Student> students4 = dao.selectStudentsByChoose(stu4);
        for (Student student : students4) {
            System.out.println(student);
        }
    }

**（3）SQL语句：**由于根据情况的不同WHERE子句只会有一个条件，所以省略and。

    <!-- 根据条件查询，使用choose标签 -->
    <select id="selectStudentsByChoose" resultType="Student">
        select id, name, age, score 
        from student 
        <where>
            <choose>
                <when test="name != null and name != ''">
                    name like '%' #{name} '%'
                </when>
                <when test="age > 0">
                    age > #{age}
                </when>
                <otherwise>
                    1 = 2
                </otherwise>
            </choose>
        </where>
    </select>

测试通过。

#### 2.6.4 &lt;foreach/&gt;标签遍历数组

foreach标签用于实现对数组和集合的遍历。

- collection属性：表示要遍历的集合的类型
- open、close、separator属性：对遍历内容的SQL拼接

需求：查询id为1、2、3的学生信息。

**（1）修改Dao接口：**

    public interface IStudentDao {
        List<Student> selectStudentsByIf(Student student);
        List<Student> selectStudentsByWhere(Student student);
        List<Student> selectStudentsByChoose(Student student);
        List<Student> selectStudentsByForeach_array(int[] ids);
    }

**（2）定义测试类：**

    @Test
    public void testSelectStudentsByForeach_array() {
        int[] ids = { 1, 2, 3 };
        List<Student> students = dao.selectStudentsByForeach(ids);
        for (Student student : students) {
            System.out.println(student);
        }
    }

**（3）SQL语句：**MyBatis定义的数组的类型别名为array

    <!-- 根据条件查询，使用foreach标签遍历数组 -->
    <select id="selectStudentsByForeach_array" resultType="Student" >
        select id, name, age, score 
        from student 
        <if test="array.length > 0">
            where id in 
            <foreach collection="array" item="element" open="(" close=")" separator="," >
                #{element}
            </foreach>
        </if>
    </select>

测试输出：

    [DEBUG][2018-01-24 22:58:18] tavish.bit.dao.IStudentDao.selectStudentsByForeach 159 ==>  Preparing: select id, name, age, score from student where id in ( ? , ? , ? ) 
    [DEBUG][2018-01-24 22:58:18] tavish.bit.dao.IStudentDao.selectStudentsByForeach 159 ==> Parameters: 1(Integer), 2(Integer), 3(Integer)
    [DEBUG][2018-01-24 22:58:18] tavish.bit.dao.IStudentDao.selectStudentsByForeach 159 <==      Total: 3
    Student [id=1, name=张三, age=23, score=93.5]
    Student [id=2, name=李四, age=24, score=94.5]
    Student [id=3, name=王五, age=25, score=95.5]

测试通过。

#### 2.6.5 &lt;foreach/&gt;标签泛型为基本类型的List

需求：查询id为1、2、3的学生信息。

**（1）修改Dao接口：**

    public interface IStudentDao {
        List<Student> selectStudentsByIf(Student student);
        List<Student> selectStudentsByWhere(Student student);
        List<Student> selectStudentsByChoose(Student student);
        List<Student> selectStudentsByForeach_array(int[] ids);
        List<Student> selectStudentsByForeach_list_1(List<Integer> ids);
    }

**（2）定义测试类：**

    @Test
    public void testSelectStudentsByForeach_list_1() {
        List<Integer> ids = new ArrayList<>();
        ids.add(1);
        ids.add(2);
        ids.add(3);
        List<Student> students = dao.selectStudentsByForeach_list_1(ids);
        for (Student student : students) {
            System.out.println(student);
        }
    }

**（3）SQL语句：**

    <select id="selectStudentsByForeach_list_1" resultType="Student" >
        select id, name, age, score 
        from student 
        <if test="list.size > 0">
            where id in 
            <foreach collection="list" item="element" open="(" close=")" separator="," >
                #{element}
            </foreach>
        </if>
    </select>

测试通过。

#### 2.6.6 &lt;foreach/&gt;标签泛型为自定义类型的List

需求：查询id为1、2、3的学生信息。

**（1）修改Dao接口：**

    public interface IStudentDao {
        List<Student> selectStudentsByIf(Student student);
        List<Student> selectStudentsByWhere(Student student);
        List<Student> selectStudentsByChoose(Student student);
        List<Student> selectStudentsByForeach_array(int[] ids);
        List<Student> selectStudentsByForeach_list_1(List<Integer> ids);
        List<Student> selectStudentsByForeach_list_2(List<Student> ids);
    }

**（2）定义测试类：**

    @Test
    public void testSelectStudentsByForeach_list_2() {
        
        List<Student> stus = new ArrayList<>();
        
        Student stu1 = new Student();
        stu1.setId(1);
        Student stu2 = new Student();
        stu2.setId(2);
        Student stu3 = new Student();
        stu3.setId(3);
        
        stus.add(stu1);
        stus.add(stu2);
        stus.add(stu3);
        
        List<Student> students = dao.selectStudentsByForeach_list_2(stus);
        for (Student student : students) {
            System.out.println(student);
        }
    }

**（3）SQL语句：**

    <select id="selectStudentsByForeach_list_2" resultType="Student" >
        select id, name, age, score 
        from student 
        <if test="list.size > 0">
            where id in 
            <foreach collection="list" item="element" open="(" close=")" separator="," >
                #{element.id}
            </foreach>
        </if>
    </select>

测试通过。

#### 2.6.7 &lt;sql/&gt;标签

&lt;sql/&gt;用于定义SQL片段，以便其它SQL标签复用。而其它标签使用该SQL片段时需要使用&lt;include/&gt;子标签。该&lt;sql/&gt;标签可以定义SQL语句中的任何部分，所以&lt;include/&gt;子标签可以放在动态SQL中的任何位置。

定义SQL片段：

    <sql id="selectStudent">
        select id, name, age, score 
        from student 
    </sql>

重写其它SQL语句：使用&lt;include/&gt;子标签对SQL片段进行引用

    <!-- 根据条件查询，使用if标签 -->
    <select id="selectStudentsByIf" resultType="Student">
        <include refid="selectStudent" />
        where 1 = 1
        <if test="name != null and name != ''">
            and name like '%' #{name} '%'
        </if>
        <if test="age > 0">
            and age > #{age}
        </if>
    </select>
    <!-- 根据条件查询，使用where标签 -->
    <select id="selectStudentsByWhere" resultType="Student">
        <include refid="selectStudent" />
        <where>
            <if test="name != null and name != ''">
                and name like '%' #{name} '%'
            </if>
            <if test="age > 0">
                and age > #{age}
            </if>
        </where>
    </select>
    <!-- 根据条件查询，使用choose标签 -->
    <select id="selectStudentsByChoose" resultType="Student">
        <include refid="selectStudent" />
        <where>
            <choose>
                <when test="name != null and name != ''">
                    name like '%' #{name} '%'
                </when>
                <when test="age > 0">
                    age > #{age}
                </when>
                <otherwise>
                    1 = 2
                </otherwise>
            </choose>
        </where>
    </select>
    <!-- 根据条件查询，使用foreach标签遍历数组 -->
    <select id="selectStudentsByForeach_array" resultType="Student">
        <include refid="selectStudent" />
        <if test="array.length > 0">
            where id in 
            <foreach collection="array" item="element" open="(" close=")" separator="," >
                #{element}
            </foreach>
        </if>
    </select>
    <!-- 根据条件查询，使用foreach标签遍历泛型为基本类型的List -->
    <select id="selectStudentsByForeach_list_1" resultType="Student" >
        <include refid="selectStudent" />
        <if test="list.size > 0">
            where id in 
            <foreach collection="list" item="element" open="(" close=")" separator="," >
                #{element}
            </foreach>
        </if>
    </select>
    <!-- 根据条件查询，使用foreach标签遍历泛型为自定义类型的List -->
    <select id="selectStudentsByForeach_list_2" resultType="Student" >
        <include refid="selectStudent" />
        <if test="list.size > 0">
            where id in 
            <foreach collection="list" item="element" open="(" close=")" separator="," >
                #{element.id}
            </foreach>
        </if>
    </select>

测试通过。

## 第三章 关联关系查询

*外键一定定义在多方表；如果一个表存在外键，则该表一定与其它表存在关联关系，并且该表在关系中为多方。*

### 3.1 关联查询

当查询内容涉及到具有关联关系的多个表时，就需要使用关链查询。根据表与表间的关联关系的不同，关联查询分为四种：

- 一对一关联查询
- 一对多关联查询
- 多对一关联查询
- 多对多关联查询

由于一对一关联查询解决方案与多对一关联查询解决方案相同，所以不做讲解。

#### 3.1.1 一对多关联查询

*源代码在MyBatis-07-relations-01-one2many下。*

**（1）定义实体类：**一方可以看到多方，多方看不到一方。

Country：

    package tavish.bit.beans;

    import java.util.Set;

    public class Country {
        private Integer cid;
        private String cname;
        // 关联属性
        private Set<Minister> ministers;

        public Integer getCid() {
            return cid;
        }

        public void setCid(Integer cid) {
            this.cid = cid;
        }

        public String getCname() {
            return cname;
        }

        public void setCname(String cname) {
            this.cname = cname;
        }

        public Set<Minister> getMinisters() {
            return ministers;
        }

        public void setMinisters(Set<Minister> ministers) {
            this.ministers = ministers;
        }

        @Override
        public String toString() {
            return "Country [cid=" + cid + ", cname=" + cname + ", ministers=" + ministers + "]";
        }
    }

Minister：

    package tavish.bit.beans;

    public class Minister {
        private Integer mid;
        private String mname;

        public Integer getMid() {
            return mid;
        }

        public void setMid(Integer mid) {
            this.mid = mid;
        }

        public String getMname() {
            return mname;
        }

        public void setMname(String mname) {
            this.mname = mname;
        }

        @Override
        public String toString() {
            return "Minister [mid=" + mid + ", mname=" + mname + "]";
        }
    }

**（2）定义数据库表：** 表之间没有外键约束。

country：

    CREATE TABLE `country` (
      `cid` int(5) NOT NULL AUTO_INCREMENT,
      `cname` varchar(20) DEFAULT NULL,
      PRIMARY KEY (`cid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8

|cid|cname|
------|-----
1|USA
2|England

minister：

    CREATE TABLE `minister` (
      `mid` int(5) NOT NULL AUTO_INCREMENT,
      `mname` varchar(20) DEFAULT NULL,
      `country_id` int(5) DEFAULT NULL,
      PRIMARY KEY (`mid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8

|mid|mname|country_id
------|-----
1|Tavish|1
2|Triela|2
3|Taco|2

**（3）定义测试类：**

    @Test
    public void testSelectCountryById() {
        Country country = dao.selectCountryById(2);
        System.out.println(country);
    }

**（4）SQL语句：**

*a）使用多表连接查询：*

    <!-- 设置resultMap对查询结果进行封装 -->
    <resultMap type="Country" id="countryMapper">
        <!-- 对Country属性进行封装 -->
        <id column="cid" property="cid" />
        <result column="cname" property="cname" />
        <!-- 对集合属性ministers进行封装 -->
        <collection property="ministers" ofType="Minister">
            <id column="mid" property="mid" />
            <result column="mname" property="mname" />
        </collection>
    </resultMap>

    <!-- 使用多表连接查询 -->
    <select id="selectCountryById" resultMap="countryMapper" >
        select cid, cname, mid, mname
        from country, minister
        where country_id = cid and cid = #{xxx}
    </select>

测试输出：执行一次select语句

    [DEBUG][2018-01-25 15:29:09] tavish.bit.dao.ICountryDao.selectCountryById 159 ==>  Preparing: select cid, cname, mid, mname from country, minister where country_id = cid and cid = ? 
    [DEBUG][2018-01-25 15:29:09] tavish.bit.dao.ICountryDao.selectCountryById 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-25 15:29:09] tavish.bit.dao.ICountryDao.selectCountryById 159 <==      Total: 2
    Country [cid=2, cname=England, ministers=[Minister [mid=3, mname=Taco], Minister [mid=2, mname=Triela]]]

*b）使用多表单独查询：*

    <!-- 设置resultMap对查询结果进行封装 -->
    <resultMap type="Country" id="countryMapper">
        <!-- 对Country属性进行封装 -->
        <id column="cid" property="cid" />
        <result column="cname" property="cname" />
        <!-- 对集合属性ministers进行封装 -->
        <collection property="ministers" 
                    ofType="Minister" 
                    select="selectMinisterByCountry" 
                    column="cid"/>
    </resultMap>
    
    <select id="selectMinisterByCountry" resultType="Minister">
        select mid, mname from minister where country_id = #{yyy}
    </select>

    <!-- 使用多表单独查询 -->
    <select id="selectCountryById" resultMap="countryMapper" >
        select cid, cname from country where cid = #{xxx}
    </select>

测试输出：执行两次select语句

    [DEBUG][2018-01-25 15:31:48] tavish.bit.dao.ICountryDao2.selectCountryById 159 ==>  Preparing: select cid, cname from country where cid = ? 
    [DEBUG][2018-01-25 15:31:48] tavish.bit.dao.ICountryDao2.selectCountryById 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-25 15:31:48] tavish.bit.dao.ICountryDao2.selectMinisterByCountry 159 ====>  Preparing: select mid, mname from minister where country_id = ? 
    [DEBUG][2018-01-25 15:31:48] tavish.bit.dao.ICountryDao2.selectMinisterByCountry 159 ====> Parameters: 2(Integer)
    [DEBUG][2018-01-25 15:31:48] tavish.bit.dao.ICountryDao2.selectMinisterByCountry 159 <====      Total: 2
    [DEBUG][2018-01-25 15:31:48] tavish.bit.dao.ICountryDao2.selectCountryById 159 <==      Total: 1
    Country [cid=2, cname=England, ministers=[Minister [mid=3, mname=Taco], Minister [mid=2, mname=Triela]]]

**
实际使用中常使用多表单独查询，因为多表单独查询支持延迟加载。
**

#### 3.1.2 多对一关联查询

多对一关联查询是指在查询多方对象时同时将其所关联的一方对象也查询出来。

由于在查询多方对象时也是一个一个查询，所以多对一关联查询其实就是一对一关联查询。即**一对一关联查询的实现方式与多对一的实现方式是相同的。**

*源代码在MyBatis-07-relations-02-many2one下。*

**（1）定义实体类：**一方看不到多方，多方可以看到一方。

Country：

    package tavish.bit.beans;

    public class Country {
        private Integer cid;
        private String cname;

        public Integer getCid() {
            return cid;
        }

        public void setCid(Integer cid) {
            this.cid = cid;
        }

        public String getCname() {
            return cname;
        }

        public void setCname(String cname) {
            this.cname = cname;
        }

        @Override
        public String toString() {
            return "Country [cid=" + cid + ", cname=" + cname + "]";
        }
    }

Minister：

    package tavish.bit.beans;

    public class Minister {
        private Integer mid;
        private String mname;
        // 关联属性
        private Country country;

        public Integer getMid() {
            return mid;
        }

        public void setMid(Integer mid) {
            this.mid = mid;
        }

        public String getMname() {
            return mname;
        }

        public void setMname(String mname) {
            this.mname = mname;
        }

        public Country getCountry() {
            return country;
        }

        public void setCountry(Country country) {
            this.country = country;
        }

        @Override
        public String toString() {
            return "Minister [mid=" + mid + ", mname=" + mname + ", country=" + country + "]";
        }
    }


**（2）定义数据库表：** 表之间没有外键约束。

同上，略。

**（3）定义测试类：**

    @Test
    public void testSelectCountryById() {
        Minister minister = dao.selectMinisterById(2);
        System.out.println(minister);
    }

**（4）SQL语句：**

*a）使用多表连接查询：*

    <resultMap type="Minister" id="ministerMapper">
        <id column="mid" property="mid"/>
        <result column="mname" property="mname"/>
        <association property="country" javaType="Country">
            <id column="cid" property="cid"/>
            <result column="cname" property="cname"/>
        </association>
    </resultMap>

    <!-- 使用多表连接查询 -->
    <select id="selectMinisterById" resultMap="ministerMapper">
        select mid, mname, cid, cname 
        from minister, country
        where country_id = cid and mid = #{xxx}
    </select>

测试输出：

    [DEBUG][2018-01-25 16:19:37] tavish.bit.dao.IMinisterDao.selectMinisterById 159 ==>  Preparing: select mid, mname, cid, cname from minister, country where country_id = cid and mid = ? 
    [DEBUG][2018-01-25 16:19:37] tavish.bit.dao.IMinisterDao.selectMinisterById 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-25 16:19:37] tavish.bit.dao.IMinisterDao.selectMinisterById 159 <==      Total: 1
    Minister [mid=2, mname=Triela, country=Country [cid=2, cname=England]]

*b）使用多表单独查询：*

    <resultMap type="Minister" id="ministerMapper">
        <id column="mid" property="mid"/>
        <result column="mname" property="mname"/>
        <association property="country"
                     javaType="Country" 
                     select="selectCountryById"
                     column="country_id"/>
    </resultMap>

    <select id="selectCountryById" resultType="Country">
        select cid, cname from country where cid = #{yyy}
    </select>

    <!-- 使用多表单独查询 -->
    <select id="selectMinisterById" resultMap="ministerMapper">
        select mid, mname, country_id from minister where mid = #{xxx}
    </select>

测试输出：

    [DEBUG][2018-01-25 16:20:07] tavish.bit.dao.IMinisterDao2.selectMinisterById 159 ==>  Preparing: select mid, mname, country_id from minister where mid = ? 
    [DEBUG][2018-01-25 16:20:07] tavish.bit.dao.IMinisterDao2.selectMinisterById 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-25 16:20:07] tavish.bit.dao.IMinisterDao2.selectCountryById 159 ====>  Preparing: select cid, cname from country where cid = ? 
    [DEBUG][2018-01-25 16:20:07] tavish.bit.dao.IMinisterDao2.selectCountryById 159 ====> Parameters: 2(Integer)
    [DEBUG][2018-01-25 16:20:07] tavish.bit.dao.IMinisterDao2.selectCountryById 159 <====      Total: 1
    [DEBUG][2018-01-25 16:20:07] tavish.bit.dao.IMinisterDao2.selectMinisterById 159 <==      Total: 1
    Minister [mid=2, mname=Triela, country=Country [cid=2, cname=England]]

#### 3.1.3 自关联查询

所谓自关联是指，自己充当一方又充当多方，是1：n或n：1的变型。

例如，对于新闻栏目NewsColumn，可与充当一方，即父栏目，也可以充当多方，即子栏目。而反映到DB表中，只有一张表，这张表中具有一个外键，用于表示该栏目的父栏目。一级栏目没有父栏目，所以可以将其外键值设为0，而子栏目具有外键值。

以下将对自关联分为两种情况：

- 一对多：即当前类作为一方，其包含多方的集合域属性。
- 多对一：即当前类作为多方，其包含一方的域属性。

##### 3.1.3.1 以一对多方式处理

*源代码在MyBatis-07-relations-03-selfRelated-one2many下。*

实现功能：

- 根据父栏目id查询指定栏目的所有子孙栏目
- 根据栏目id查询指定栏目

**（1）定义实体类：**

    package tavish.bit.beans;

    import java.util.Set;

    // 当前的新闻栏目作为一方，即父栏目
    public class NewsLabel {
        private Integer id;
        private String name;
        private Set<NewsLabel> children;

        public Integer getId() {
            return id;
        }

        public void setId(Integer id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public Set<NewsLabel> getChildren() {
            return children;
        }

        public void setChildren(Set<NewsLabel> children) {
            this.children = children;
        }

        @Override
        public String toString() {
            return "NewsLabel [id=" + id + ", name=" + name + ", children=" + children + "]";
        }
    }


**（2）定义数据表：**

    CREATE TABLE `newslabel` (
      `id` int(5) NOT NULL AUTO_INCREMENT,
      `name` varchar(20) DEFAULT NULL,
      `pid` int(5) DEFAULT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8

id|name|pid
-----|-----|-----
1|娱乐新闻|0
2|体育新闻|0
3|NBA|2
4|CBA|2
5|火箭|3
6|湖人|3
7|北京金隅|4
8|浙江广厦|4
9|青岛双星|4
10|港台明星|1
11|内地明星|1

**（3）定义Dao接口：**

    package tavish.bit.dao;

    import java.util.List;

    import tavish.bit.beans.NewsLabel;

    public interface INewsLabelDao {
        // 查询指定栏目的所有子孙栏目
        List<NewsLabel> selectChildrenByParent(int pid);
        
        // 查询指定栏目
        NewsLabel selectNewsLabelById(int id);
    }

**（4）定义测试方法：**

    // 测试自关联查询---以一对多方式处理---根据父栏目id查询指定栏目的所有子孙栏目
    @Test
    public void testSelectChildrenByParent() {
        List<NewsLabel> children = dao.selectChildrenByParent(2);
        for (NewsLabel newsLabel : children) {
            System.out.println(newsLabel);
        }
    }
    
    // 测试自关联查询---以一对多方式处理---根据栏目id查询指定栏目
    @Test
    public void testSelectNewsLabelById() {
        NewsLabel newsLabel = dao.selectNewsLabelById(2);
        System.out.println(newsLabel);
    }

**（5）SQL语句：**

    <resultMap type="NewsLabel" id="newslabelMapper">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <!-- 递归查询 -->
        <collection property="children"
                    ofType="NewsLabel"
                    select="selectChildrenByParent"
                    column="id" />
    </resultMap>
    
    <!-- 根据父栏目id查询指定栏目的所有子孙栏目 -->
    <select id="selectChildrenByParent" resultMap="newslabelMapper">
        select id, name from newslabel where pid = #{xxx}
    </select>
    
    <!-- 根据栏目id查询指定栏目 -->
    <select id="selectNewsLabelById" resultMap="newslabelMapper">
        select id, name from newslabel where id = #{xxx}
    </select>

测试输出：

根据父栏目id查询指定栏目的所有子孙栏目：

    NewsLabel [id=3, name=NBA, children=[NewsLabel [id=6, name=湖人, children=[]], NewsLabel [id=5, name=火箭, children=[]]]]
    NewsLabel [id=4, name=CBA, children=[NewsLabel [id=7, name=北京金隅, children=[]], NewsLabel [id=8, name=浙江广厦, children=[]], NewsLabel [id=9, name=青岛双星, children=[]]]]

根据栏目id查询指定栏目：

    NewsLabel [id=2, name=体育新闻, children=[NewsLabel [id=3, name=NBA, children=[NewsLabel [id=5, name=火箭, children=[]], NewsLabel [id=6, name=湖人, children=[]]]], NewsLabel [id=4, name=CBA, children=[NewsLabel [id=9, name=青岛双星, children=[]], NewsLabel [id=7, name=北京金隅, children=[]], NewsLabel [id=8, name=浙江广厦, children=[]]]]]]

##### 3.1.3.2 以多对一方式处理

*源代码在MyBatis-07-relations-04-selfRelated-many2one下。*

需求：根据id查询当前栏目及其所有父栏目

**（1）定义实体类：**

    package tavish.bit.beans;

    // 当前的新闻栏目作为多方，即子栏目
    public class NewsLabel {
        private Integer id;
        private String name;
        private NewsLabel parent;

        public Integer getId() {
            return id;
        }

        public void setId(Integer id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public NewsLabel getParent() {
            return parent;
        }

        public void setParent(NewsLabel parent) {
            this.parent = parent;
        }

        @Override
        public String toString() {
            return "NewsLabel [id=" + id + ", name=" + name + ", parent=" + parent + "]";
        }
    }

**（2）定义数据表：**

同上，略。

**（3）定义Dao接口：**

    package tavish.bit.dao;

    import tavish.bit.beans.NewsLabel;

    public interface INewsLabelDao {    
        
        // 查询指定栏目及其父栏目
        NewsLabel selectNewsLabelById(int id);
    }


**（4）定义测试方法：**

    // 测试自关联查询---以多对一方式处理---根据栏目id查询指定栏目及其父栏目
    @Test
    public void testSelectNewsLabelById() {
        NewsLabel newsLabel = dao.selectNewsLabelById(9);
        System.out.println(newsLabel);
    }

**（5）SQL语句：**

    <resultMap type="NewsLabel" id="newslabelMapper">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <!-- 递归查询 -->
        <association property="parent" 
                     javaType="NewsLabel"
                     select="selectNewsLabelById"
                     column="pid" />
    </resultMap>
    
    <!-- 根据栏目id查询指定栏目及其父栏目 -->
    <select id="selectNewsLabelById" resultMap="newslabelMapper">
        select id, name, pid from newslabel where id = #{xxx}
    </select>

测试输出：

    NewsLabel [id=9, name=青岛双星, parent=NewsLabel [id=4, name=CBA, parent=NewsLabel [id=2, name=体育新闻, parent=null]]]

#### 3.1.4 多对多查询

*源代码在MyBatis-07-relations-05-many2many下。*

所谓的多对多关系实际是由两个互反的一对多关系组成的。

多对多关联关系举例：一个学生可以选择多门课程，而一门课程也可以由多个学生选择。

通常情况下，多对多关系都会通过一个中间表来建立。

**（1）定义实体：**

Student:

    package tavish.bit.beans;

    import java.util.Set;

    public class Student {
        private Integer sid;
        private String sname;
        private Set<Course> courses;

        public Integer getSid() {
            return sid;
        }

        public void setSid(Integer sid) {
            this.sid = sid;
        }

        public String getSname() {
            return sname;
        }

        public void setSname(String sname) {
            this.sname = sname;
        }

        public Set<Course> getCourses() {
            return courses;
        }

        public void setCourses(Set<Course> courses) {
            this.courses = courses;
        }

        @Override
        public String toString() {
            return "Student [sid=" + sid + ", sname=" + sname + ", courses=" + courses + "]";
        }
    }

Course:

    package tavish.bit.beans;

    import java.util.Set;

    public class Course {

        private Integer cid;
        private String cname;
        private Set<Student> students;

        public Integer getCid() {
            return cid;
        }

        public void setCid(Integer cid) {
            this.cid = cid;
        }

        public String getCname() {
            return cname;
        }

        public void setCname(String cname) {
            this.cname = cname;
        }

        public Set<Student> getStudents() {
            return students;
        }

        public void setStudents(Set<Student> students) {
            this.students = students;
        }

        @Override
        public String toString() {
            return "Course [cid=" + cid + ", cname=" + cname + "]";
        }
    }

**（2）定义数据表：**

student：作为一方

    CREATE TABLE `student` (
      `sid` int(5) NOT NULL AUTO_INCREMENT,
      `sname` varchar(20) DEFAULT NULL,
      PRIMARY KEY (`sid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8

sid|sname
-----|-----
1|张三
2|李四
3|王五

course：作为一方

    CREATE TABLE `course` (
      `cid` int(5) NOT NULL AUTO_INCREMENT,
      `cname` varchar(20) DEFAULT NULL,
      PRIMARY KEY (`cid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8

cid|cname
-----|-----
1|JavaSE
2|JavaEE
3|Android

middle：中间表，用于关联student表和course表，作为多方。

    CREATE TABLE `middle` (
      `id` int(5) NOT NULL AUTO_INCREMENT,
      `student_id` int(5) DEFAULT NULL,
      `course_id` int(5) DEFAULT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8

id|student_id|course_id
-----|-----|-----
1|1|1
2|1|2
3|2|1
4|2|3
5|3|1
6|3|2
7|3|3

**（3）定义Dao接口：**由于多对多的双方完全平等，所以选择其中一方进行查询即可，这里选择学生。

    public interface IStudentDao {
        Student selectStudentById(int sid);
    }

**（4）定义测试类：**

    // 测试多对多关联查询---使用多表连接查询
    @Test
    public void testSelectCountryById() {
        Student student = dao.selectStudentById(2);
        System.out.println(student);
    }

**（5）SQL语句：**

    <resultMap type="Student" id="studentMapper">
        <id column="sid" property="sid"/>
        <result column="sname" property="sname"/>
        <collection property="courses" 
                    ofType="Course">
            <id column="cid" property="cid"/>
            <result column="cname" property="cname" />
        </collection>
    </resultMap>

    <!-- 使用多表连接查询 -->
    <select id="selectStudentById" resultMap="studentMapper">
        select sid, sname, cid, cname 
        from student, middle, course
        where sid = student_id and cid = course_id and sid = #{xxx}
    </select>

测试输出：

    Student [sid=2, sname=李四, courses=[Course [cid=3, cname=Android], Course [cid=1, cname=JavaSE]]]


## 3.2 延迟加载

MyBatis中的延迟加载，也称为懒加载，是指在进行关联查询时，按照设置延迟规则推迟对关联属性的select查询。延迟加载可以有效的减少数据库压力。

需要注意的是，**MyBatis的延迟加载只是对关联属性的查询有延迟设置**，对于主加载对象都是直接执行查询语句的。

### 3.2.1 关联属性加载时机

MyBatis根据对关联属性查询的select语句的执行时机，分为三种类型：直接加载、侵入式延迟加载与深度延迟加载。

- **直接加载：**执行完对主加载对象的select语句，马上执行对关联属性的select查询。
- **侵入式延迟加载：**执行对主加载对象的查询时，不会执行对关联属性的查询。但当要访问主加载对象的详情时，就会马上执行关联属性的select查询。即对关联属性的查询执行，侵入到了主加载对象的详情访问中。也可以这样理解：将关联属性的详情侵入到了主加载对象的详情中，即将关联属性的详情作为主加载对象的详情的一部分出现了。
- **深度延迟加载：**执行对主加载对象的查询时，不会执行对关联属性的查询。访问主加载对象的详情时也不会执行关联属性的select查询。只有当真正访问关联属性的详情时，才会执行对关联属性的select查询。

**需要注意的是，延迟加载的应用要求，关联属性的查询与主加载对象的查询必须是分别进行的select语句，不能是使用多表连接所进行的select查询。**因为多表连接查询，实际上是对一张表的查询，对由多个表连接后形成的一张表的查询，这回一次性将多张表的所有信息查询出来。

MyBatis中对于延迟加载的设置可以应用到一对一、一对多、多对一、多对多的所有关联关系查询中。

以下以一对多关联关系查询为例。

#### 3.2.1.1 直接加载

*源代码在MyBatis-08-lazy-01-instantlyload下。*

Dao接口、测试类、SQL语句与一对多关联查询相同，查询使用多表单独查询。

在测试类加入断点：

    @Test
    public void testSelectCountryById() {
        // 加入断点
        Country country = dao.selectCountryById(2);
        System.out.println(country.getCname());
    }

以debug模式运行：

当执行selectCountryById方法时，会立即输出：

    [DEBUG][2018-01-26 11:41:24] tavish.bit.dao.ICountryDao.selectCountryById 159 ==>  Preparing: select cid, cname from country where cid = ? 
    [DEBUG][2018-01-26 11:41:25] tavish.bit.dao.ICountryDao.selectCountryById 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-26 11:41:27] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 ====>  Preparing: select mid, mname from minister where country_id = ? 
    [DEBUG][2018-01-26 11:41:27] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 ====> Parameters: 2(Integer)
    [DEBUG][2018-01-26 11:41:27] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 <====      Total: 2
    [DEBUG][2018-01-26 11:41:27] tavish.bit.dao.ICountryDao.selectCountryById 159 <==      Total: 1

此时，两个select语句均得到执行，既查询了主加载对象，又查询了关联属性。

#### 3.2.1.2 侵入式延迟加载

*源代码在MyBatis-08-lazy-02-agressivelazyloading下。*

开启侵入式延迟加载需要修改主配置文件，将延迟加载开关lazyLoadingEnabled设置为true，同时将侵入式延迟加载开关aggressiveLazyLoading也设置为true。

    <!-- 设置整个应用所使用的常量 -->
    <settings>
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="aggressiveLazyLoading" value="true"/>
    </settings>

执行测试，以debug模式运行：

    @Test
    public void testSelectCountryById() {
        // 加入断点
        Country country = dao.selectCountryById(2);
        System.out.println(country.getCname());
    }

当执行selectCountryById方法时，会立即输出：

    [DEBUG][2018-01-26 11:55:24] tavish.bit.dao.ICountryDao.selectCountryById 159 ==>  Preparing: select cid, cname from country where cid = ? 
    [DEBUG][2018-01-26 11:55:25] tavish.bit.dao.ICountryDao.selectCountryById 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-26 11:55:27] tavish.bit.dao.ICountryDao.selectCountryById 159 <==      Total: 1

此时只对主加载对象进行了查询，没有对关联属性进行查询。

当执行到System.out.println(country.getCname())时输出：

    [DEBUG][2018-01-26 11:56:58] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 ==>  Preparing: select mid, mname from minister where country_id = ? 
    [DEBUG][2018-01-26 11:56:58] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-26 11:56:58] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 <==      Total: 2
    England

这时进行了关联属性的查询。这是因为该语句访问了主加载对象的详情属性（getCname()），所以根据侵入式延迟加载，此时进行对关联属性的查询。

#### 3.2.1.3 深度延迟加载

*源代码在MyBatis-08-lazy-03-deeplazyloading下。*

开启深度延迟加载需要修改主配置文件，将延迟加载开关lazyLoadingEnabled设置为true，同时将侵入式延迟加载开关aggressiveLazyLoading也设置为false。

    <!-- 设置整个应用所使用的常量 -->
    <settings>
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="aggressiveLazyLoading" value="false"/>
    </settings>

修改测试类：

    // 测试一对多关联查询---使用多表单独查询---深度延迟
    @Test
    public void testSelectCountryById() {
        // 加入断点
        Country country = dao.selectCountryById(2);
        System.out.println(country.getCname());
        System.out.println(country);
    }

执行测试，以debug模式运行：

当执行selectCountryById方法时输出：

    [DEBUG][2018-01-26 12:16:42] tavish.bit.dao.ICountryDao.selectCountryById 159 ==>  Preparing: select cid, cname from country where cid = ? 
    [DEBUG][2018-01-26 12:16:44] tavish.bit.dao.ICountryDao.selectCountryById 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-26 12:16:45] tavish.bit.dao.ICountryDao.selectCountryById 159 <==      Total: 1

此时进行了主加载对象的查询。

当执行输出country.getCname()时输出：

    England

此时仅仅对主加载对象的详情进行了输出，由于不涉及关联属性，所以此时没有对关联属性进行查询。

当执行输出country时输出：

    [DEBUG][2018-01-26 12:19:19] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 ==>  Preparing: select mid, mname from minister where country_id = ? 
    [DEBUG][2018-01-26 12:19:19] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 ==> Parameters: 2(Integer)
    [DEBUG][2018-01-26 12:19:19] tavish.bit.dao.ICountryDao.selectMinisterByCountry 159 <==      Total: 2
    Country [cid=2, cname=England, ministers=[Minister [mid=2, mname=Triela], Minister [mid=3, mname=Taco]]]

此时由于打印country对象会同时对关联属性进行访问，所以根据深度延迟加载，此时进行关联属性的查询。

#### 3.2.1.4 延迟加载策略总结

- lazyLoadingEnabled：默认值false，延迟加载的总开关。
- aggressiveLazyLoading：默认值true，侵入式延迟加载开关。

加载策略|lazyLoadingEnabled|aggressiveLazyLoading
-----|-----|-----
直接加载（缺省配置）|false|true/false
侵入式延迟加载|true|true
深度延迟加载|true|false

## 第四章 查询缓存

查询缓存的使用，主要是为了提高查询访问速度。将用户对统一数据的重复查询过程简化，不再每次均从数据库查询获取结果数据，从而提高访问速度。

MyBatis的查询缓存机制，根据缓存区的作用域（生命周期）可划分为两种：一级查询缓存与二级查询缓存。

MyBatis查询缓存的作用域是根据映射文件mapper的namespace划分的，相同namespace的mapper查询数据存放在同一个缓存区域。不同namespace下的数据互不干扰。无论是一级缓存还是二级缓存，都是按照namespace进行分别存放的。



### 4.1 一级查询缓存

MyBatis一级查询缓存是基于HashMap的本地缓存。其作用域是SqlSession。在同一个SqlSession中两次执行相同的sql查询语句，第一次执行完毕后，会将查询结果写入缓存，第二次会从缓存中直接获取数据，而不再到数据库中进行查询，从而提高查询效率。

当一个SqlSession结束后，该SqlSession中的一级查询缓存也就不存在了。

**MyBatis默认一级查询缓存是开启状态，且不能关闭。**

*源代码在MyBatis-09-cache-01-sqlSessionCache下。*

#### 4.1.1 证明一级缓存的存在

测试查询：对同一id的select语句执行两次查询

    @Test
    public void testSelectStudentById() {
        Student student = dao.selectStudentById(3);
        Student student2 = dao.selectStudentById(3);
        System.out.println(student);
        System.out.println(student2);
    }

输出：

    [DEBUG][2018-01-26 13:57:14] tavish.bit.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 13:57:14] tavish.bit.dao.IStudentDao.selectStudentById 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 13:57:14] tavish.bit.dao.IStudentDao.selectStudentById 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]
    Student [id=3, name=Taco, age=23, score=95.0]

两次相同的查询只执行了第一次，证明一级缓存确实存在。

#### 4.1.2 缓存查找依据

**一级缓存缓存的是相同SQL映射id和相同SQL语句的查询结果。**因为MyBatis内部对于查询缓存其底层均使用一个HashMap实现：key为SQL语句的id，value为从数据库中查询出的结果。

**（1）修改Dao接口：**提供两个相同实现的方法

    // 根据id查询
    Student selectStudentById(int id);
    
    // 根据id查询2
    Student selectStudentById2(int id);

**（2）修改测试类：**

    @Test
    public void testSelectStudentById() {
        Student student = dao.selectStudentById(3);
        System.out.println(student);
        Student student2 = dao.selectStudentById2(3);
        Student student3 = dao.selectStudentById2(3);
        Student student4 = dao.selectStudentById2(4);
        System.out.println(student2);
        System.out.println(student3);
        System.out.println(student4);
    }

**（3）SQL语句：**使两个SQL语句具有不同的id，但具体语句相同

    <!-- 根据id查询一个学生 --> 
    <select id="selectStudentById" resultType="Student">
        select id, name, age, score from student where id=#{xxx}
    </select>
    
    <!-- 根据id查询一个学生2 -->    
    <select id="selectStudentById2" resultType="Student">
        select id, name, age, score from student where id=#{xxx}
    </select>

测试输出：

    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]
    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById2 159 <==      Total: 1
    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==> Parameters: 4(Integer)
    [DEBUG][2018-01-26 14:36:18] tavish.bit.dao.IStudentDao.selectStudentById2 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]
    Student [id=3, name=Taco, age=23, score=95.0]
    Student [id=4, name=赵三六, age=26, score=93.6]

由于前两次查询所用的SQL语句id不同，所以均执行了select语句。第三次查询由于其执行的SQL语句的id及具体的SQL语句与第二次相同，所以直接使用了缓存中的内容。第四次查询虽然使用的SQL语句的id与第二次、第三次相同，但是由于其具体的SQL语句不同，所以也执行了select查询。

#### 4.1.3 增删改方法对缓存的影响

增删改操作，无论是否进行sqlSession.commit()，均会清空一级缓存，使查询再次从DB中select。

测试方法：

    @Test
    public void testSelectStudentById2() {
        
        Student student = dao.selectStudentById(3);
        System.out.println(student);
        Student student2 = dao.selectStudentById2(3);
        
        dao.insertStudent(new Student("赵六", 26, 93.6));
        
        Student student3 = dao.selectStudentById2(3);
        Student student4 = dao.selectStudentById2(4);
        System.out.println(student2);
        System.out.println(student3);
        System.out.println(student4);
    }

输出：

    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 <==      Total: 1
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.insertStudent 159 ==>  Preparing: insert into student(name,age,score) values(?,?,?) 
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.insertStudent 159 ==> Parameters: 赵六(String), 26(Integer), 93.6(Double)
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.insertStudent 159 <==    Updates: 1
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 <==      Total: 1
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 ==> Parameters: 4(Integer)
    [DEBUG][2018-01-26 14:47:41] tavish.bit.dao.IStudentDao.selectStudentById2 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]
    Student [id=3, name=Taco, age=23, score=95.0]
    Student [id=4, name=赵三六, age=26, score=93.6]

由于在第二次和第三次查询中调用插入方法，导致一级缓存被清空，所以第三次查询无法从缓存中得到对象，故进行了select查询。

### 4.2 二级查询缓存

二级缓存的生命周期会与整个应用同步，与SqlSession是否关闭无关，换句话说，一级缓存是在统一线程（同一SqlSession）间共享数据，而二级缓存是在不同线程（不同SqlSession）间共享数据。

使用二级缓存的目的不是共享数据，因为MyBatis从缓存中读取数据的依据是SQL的id和具体的SQL语句，而非查询出的对象。所以二级缓存中的数据不是为了在多个查询之间共享，而是为了延长该查询结果的保存事件，提高系统性能。


#### 4.2.1 内置二级缓存

*源代码在MyBatis-09-cache-02-2ndCache-buildin下。*

MyBatis内置的二级缓存为org.apache.ibatis.chache.impl.PerpetualCache

##### 4.2.1.1 二级缓存的用法

二级查询缓存的使用很简单，只需要完成两步即可：

**（1）实体序列化：**要求查询结果所涉及到的实体类要实现Serializable接口。若该类存在父类，或其具有域属性，则父类与域属性也要实现Serialiable接口。

    public class Student implements Serializable {
        private static final long serialVersionUID = -7442393952399863075L;
        
        private Integer id;
        private String name;
        private int age;
        private double score;
        // ...
    }

**（2）mapper映射中添加&lt;cache&gt;标签：**此时该mapper文件下映射的SQL语句将使用二级缓存。

    <mapper namespace="tavish.bit.dao.IStudentDao">
        
        <cache />
        ...
    <mapper/>


##### 4.2.1.2 证明二级缓存的存在

测试方法：

    // 证明二级缓存的存在
    @Test
    public void test2ndCache() {
        Student student = dao.selectStudentById(3);
        System.out.println(student);
        
        // 关闭SqlSession，此时一级缓存被清空。
        sqlSession.close();
        
        // 重新获取SqlSession
        sqlSession = MyBatisUtils.getSqlSession();
        dao = sqlSession.getMapper(IStudentDao.class);
        
        Student student2 = dao.selectStudentById(3);
        System.out.println(student2);
    }

输出：

    [DEBUG][2018-01-26 22:03:29] tavish.bit.dao.IStudentDao 62 Cache Hit Ratio [tavish.bit.dao.IStudentDao]: 0.0
    [DEBUG][2018-01-26 22:03:29] tavish.bit.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 22:03:29] tavish.bit.dao.IStudentDao.selectStudentById 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 22:03:29] tavish.bit.dao.IStudentDao.selectStudentById 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]
    [DEBUG][2018-01-26 22:03:29] tavish.bit.dao.IStudentDao 62 Cache Hit Ratio [tavish.bit.dao.IStudentDao]: 0.5
    Student [id=3, name=Taco, age=23, score=95.0]

此时只进行了一次查询，第二次使用selectStudentById方法没有进行select查询，而是从二级缓存中读取的数据。

##### 4.2.1.3 增删改对二级缓存的影响

测试方法：

    @Test
    public void test2ndCache_2() {
        Student student = dao.selectStudentById(3);
        System.out.println(student);
        
        // 关闭SqlSession，此时一级缓存被清空。
        sqlSession.close();
        
        // 重新获取SqlSession
        sqlSession = MyBatisUtils.getSqlSession();
        dao = sqlSession.getMapper(IStudentDao.class);
        
        // 增加一次insert操作
        dao.insertStudent(new Student("Terry", 20, 90.0));
        
        Student student2 = dao.selectStudentById(3);
        System.out.println(student2);
    }

输出：

    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao 62 Cache Hit Ratio [tavish.bit.dao.IStudentDao]: 0.0
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.selectStudentById 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.selectStudentById 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.insertStudent 159 ==>  Preparing: insert into student(name,age,score) values(?,?,?) 
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.insertStudent 159 ==> Parameters: Terry(String), 20(Integer), 90.0(Double)
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.insertStudent 159 <==    Updates: 1
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao 62 Cache Hit Ratio [tavish.bit.dao.IStudentDao]: 0.5
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.selectStudentById 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 22:13:59] tavish.bit.dao.IStudentDao.selectStudentById 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]

在做了insert操作之后，第二次查询执行了select语句。所以此时二级缓存被清空，**即增删改操作确实会清空二级缓存。**但此时缓存命中率为0.5，表明确实从二级缓存中找到了对应的数据，这是为什么？

这是因为，缓存的清空，清空的是键值对中的值（置为null），其键还存在于Map中，缓存命中率计算的是是否查找到了key。所以此时在缓存中确实查找到了对应的key，但是其对应的value是null，所以要进行查询。同时因为确实在缓存中查找到了key，所以此时缓存命中率为0.5。

##### 4.2.1.4 设置增删改操作不刷新二级缓存

若要使某个增删改操作不清空二级缓存，需要在其标签上添加属性flushCache="false"，该属性默认为true。

    <update id="updateStudentById" flushCache="false">
        update student set name=#{name}, age=#{age}, score=#{score} where id=#{id}
    </update>

##### 4.2.1.5 配置二级缓存

为cache标签添加一些相关属性设置，可以对二级缓存的运行性能进行控制。当然如果不指定设置，则均保持默认值。

    <cache eviction="FIFO" flushInterval="10800000"
            readOnly="true" size="512" />

- eviction：逐出策略。当二级缓存中的对象达到最大值时，就需要通过逐出策略将缓存中的对象移除缓存，默认为LRU。常用策略有：
    + FIFO：First In First Out，先进先出策略。
    + LRU：Least Recently Used，未被使用时间最长的。
- flushInterval：刷新缓存的事件间隔，单位ms。这里的刷新缓存即清空缓存。一般不指定，即当执行增删改时刷新缓存。
- readOnly：设置缓存中数据是否只读。只读的缓存会给所有调用者返回缓存对象的相同实例，因此这些对象不能被修改。者提供了很重要的性能优势，但读写的缓存会返回缓存对象的拷贝，这会慢一些，但是更安全，因此默认为false。
- size：二级缓存中可以存放的最多对象个数，默认是1024个。

##### 4.2.1.6 二级缓存的关闭

**（1）全局关闭：**

所谓全局关闭是指，整个应用的二级缓存全部关闭，所有查询均不适用二级缓存。全局开关设置在主配置文件的全局设置settings标签中，该属性为cacheEnabled，设置为false，色关闭；设置为true，则开启，默认值为true。即二级缓存默认是开启的。

    <settings>
        <setting name="cacheEnabled" value="false"/>
    </settings>

**（2）局部关闭：**

所谓局部关闭是指，对于整个应用来说二级缓存是开启的，但只是针对某个select查询，不适用二级缓存。此时可以单独至关闭该select标签的二级缓存。

在要关闭二级缓存的select标签中将其useCache标签设置为false，即可关闭该查询的二级缓存。该属性默认为true。

    <select id="..." useCache="false" resultType="...">
        ...
    </select>

##### 4.2.1.6 二级缓存的使用原则

1. **多个namespace不要操作同一张表：**由于二级缓存中的数据是基于namespace的，即不同namespace中的数据互不干扰。若某个用户在某个namespace下对表执行了增删改操作，该操作只会引发当前namespace下的二级缓存的刷新，而对其它namespace下的二级缓存没有影响。这样的话，其它二级缓存中的数据依然是未更新的数据，也就出现了多个namespace中数据不一致的现象。
2. **不要再关联关系表上执行增删改操作：**一个namespace一般是对同一个表进行操作，若表间存在关联关系，也就意味着同一个表可能会出现在多个namespace中。这样就存在一个风险，若某一个namespace中对一个表进行增删改操作时影响到了其关联表的数据，而这个表的数据的修改只会刷新当前namespace下的二级缓存，而对另一namespace下的二级缓存数据没有影响。 这也是多个namespace对统一张表的操作。
3. **查询多余修改时使用二级缓存：**在查询操作远远多于增删改操作的情况下可以使用二级缓存。因为任何增删改操作都会刷新二级缓存，对二级缓存的频繁刷新将降低系统性能。


#### 4.2.2 ehcache二级缓存

*源代码在MyBatis-09-cache-02-2ndCache-ehcache下。*

MyBatis的特长是SQL操作，缓存数据管理不是其特长，为了提高缓存的性能，MyBatis允许使用第三方缓存产品，ehcache就是其中一种。

**使用ehcache二级缓存，实体类无需实现序列化接口。**

使用ehcache需要增加Jar包：

- ehcache-core-2.6.8.jar
- mybatis-ehcache-1.0.3.jar
- slf4j-api-1.7.25.jar

##### 4.2.2.1 ehcache的开启

在mapper中的cache标签中指定type属性：

    <!-- 指定使用ehcache二级缓存 -->
    <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>

在src目录下添加ehcache的配置文件：ehcache.xml

证明二级缓存的存在：

    @Test
    public void test2ndCache() {
        Student student = dao.selectStudentById(3);
        System.out.println(student);
        
        // 关闭SqlSession，此时一级缓存被清空。
        sqlSession.close();
        
        // 重新获取SqlSession
        sqlSession = MyBatisUtils.getSqlSession();
        dao = sqlSession.getMapper(IStudentDao.class);
        
        Student student2 = dao.selectStudentById(3);
        System.out.println(student2);
    }

测试输出：

    [DEBUG][2018-01-26 23:17:43] tavish.bit.dao.IStudentDao 62 Cache Hit Ratio [tavish.bit.dao.IStudentDao]: 0.0
    [DEBUG][2018-01-26 23:17:43] tavish.bit.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id, name, age, score from student where id=? 
    [DEBUG][2018-01-26 23:17:43] tavish.bit.dao.IStudentDao.selectStudentById 159 ==> Parameters: 3(Integer)
    [DEBUG][2018-01-26 23:17:43] tavish.bit.dao.IStudentDao.selectStudentById 159 <==      Total: 1
    Student [id=3, name=Taco, age=23, score=95.0]
    [DEBUG][2018-01-26 23:17:43] tavish.bit.dao.IStudentDao 62 Cache Hit Ratio [tavish.bit.dao.IStudentDao]: 0.5
    Student [id=3, name=Taco, age=23, score=95.0]

输出证明二级缓存存在，说明ehcache已经开启。

##### 4.2.2.2 ehcache的配置

**（1）&lt;diskStore/&gt;标签：**

指定一个文件目录，当内存空间不够，需要将二级缓存中的数据写到硬盘上时，会写到这个指定目录中。其值默认为java.io.tmpdir，表示当前系统的默认文件临时目录。

    <diskStore path="java.io.tmpdir"/>

当前系统的默认文件临时目录，可以通过System.getProperty()查看：

    String path = System.getProperty("java.io.tmpdir");
    System.out.println(path);

**（2）&lt;defaultCache/&gt;标签：**默认管理策略。

    <defaultCache
        maxElementsInMemory="10000"
        eternal="false"
        timeToIdleSeconds="120"
        timeToLiveSeconds="120"
        maxElementsOnDisk="10000000"
        diskExpiryThreadIntervalSeconds="120"
        memoryStoreEvictionPolicy="LRU" 
    </defaultCache>

+ **maxElementsInMemory**：指定该内存缓冲区可以存放缓存对象的最多个数。
+ **eternal**：设定缓存对象是否不会过期。如果设为true，表示对象永远不会过期，此时会忽略timeToIdleSeconds、timeToLiveSeconds的相关设置。默认值为false。
+ **timeToIdleSeconds**：设定允许对象处于空闲状态的最擦汗给你时间，以秒为单位。当对象自从最近一次访问后，若处于空闲状态的时间超过了该设定值，这个对象就会过期。当对象过期，EHCache就会将它从缓存清除。设置值为0，则对象可以无限期地处于空闲状态。
+ **timeToLiveSeconds**：设定对象允许存在于缓存中的最长时间，以秒为单位。当对象自从被存放到缓存后，若处于缓存中的时间超过了该设定值，这个对象就会过期。当对象过期，EHCache就会将它从缓存中清除。设置值为0，则对象可以无限期地存在于缓存中。注意只有timeToLiveSeconds≥timeToIdleSeconds，才有意义。
+ **maxElementsOnDisk**：指定硬盘缓存区可以存放缓存对象的最多个数。
+ **diskExpiryThreadIntervalSeconds**：指定硬盘中缓存对象失效时间间隔。
+ **memoryStoreEvictionPolicy**：如果内存缓存区超出限制，选择移向硬盘缓存区中的对象时使用的策略。其支持策略有：
    * **FIFO**：First in First Out，先进先出。
    * **LFU**：Less Frequently Used，使用频率最小的。
    * **LRU**：Least Recently Used，未被使用时间最长的。

**&lt;persistence/&gt;子标签：**表示内存缓存对象达到上限，会将溢出的对象写到硬盘临时目录缓存中。

    <persistence strategy="localTempSwap"/>

上述策略表示此时会将缓存内容写入diskStore标签设置的磁盘目录中。

##### 4.2.2.3 ehcache在不同mapper中的个性化配置

在ehcache.xml中设置的属性值，会将该项目中所有使用ehcache缓存机制的缓存区域起作用。一个项目中有多个mapper，不同的mapper有不同的缓存区域。对于不同缓存区域也可进行专门针对于当前区域的个性化设置，可通过指定不同mapper的&lt;cache/&gt;属性值来设置。

&lt;cache/&gt;属性值优先级高于ehcache.xml中的属性值。

示例：

    <cache>
        <property name="maxElementsInMemory" value="5000"/>
        <property name="timeToIdleSeconds" value="240"/>
    </cache>

## 第五章 MyBatis注解式开发

MyBatis的注解主要是用于替换映射文件。而映射文件中无非存放着增删改查操作的SQL映射标签。所以MyBatis注解就是要替换映射文件中的SQL标签。

MyBatis官方文档中指出，若要真正发挥MyBatis的功能，还是要用映射文件。即MyBatis官方并不建议通过注解方式来使用MyBatis。

### 5.1 MyBatis注解

- @Insert：其value属性用于指定要执行的insert语句。
- @SelectKey：用于替换XML中的selectKey标签，用于返回新插入数据的id值。
    + statement：获取新插入记录主键值的SQL语句。
    + keyProperty：获取的该主键值返回后初始化对象的哪个属性。
    + resultType：返回值类型
- @Delete：其value属性用于指定要执行的delete语句。
- @Update：其value属性用于指定要执行的update语句。
- @Select：其value属性用于指定要执行的select语句。

### 5.2 MyBatis注解使用

*源代码在MyBatis-10-annotation下。*

删除mapper.xml，在Dao接口上添加注解：

    package tavish.bit.dao;

    import java.util.List;

    import org.apache.ibatis.annotations.Delete;
    import org.apache.ibatis.annotations.Insert;
    import org.apache.ibatis.annotations.Select;
    import org.apache.ibatis.annotations.SelectKey;
    import org.apache.ibatis.annotations.Update;

    import tavish.bit.beans.Student;

    public interface IStudentDao {
        // 插入
        @Insert("insert into student(name,age,score) values(#{name},#{age},#{score})")
        void insertStudent(Student student);
        
        // 插入后当前对象自动获得id
        @Insert("insert into student(name,age,score) values(#{name},#{age},#{score})")
        @SelectKey(before = false, // before = false代表order="After"
                   keyProperty = "id", 
                   resultType = Integer.class, 
                   statement = { "select @@identity" })
        void insertStudentCacheId(Student student);
        
        // 根据id删除
        @Delete("delete from student where id=#{xxx}")
        void deleteStudentById(int id);
        
        // 修改
        @Update("update student set name=#{name}, age=#{age}, score=#{score} where id=#{id}")
        void updateStudentById(Student student);

        // 查询所有---List
        @Select("select id, name, age, score from student")
        List<Student> selectAllStudents();
        
        // 根据id查询
        @Select("select id, name, age, score from student where id=#{xxx}")
        Student selectStudentById(int id);
        
        // 根据名字模糊查询
        @Select("select id, name, age, score from student where name like '%' #{xxx} '%'")
        List<Student> selectStudentsByName(String name);
    }

注册映射：使用package标签。

    <mappers>
        <package name="tavish.bit.dao"/>
    </mappers>

测试通过。