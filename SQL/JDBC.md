[TOC]

# JDBC笔记

## 数据持久化

- 持久化：把数据保存到可掉电式存储设备中以供之后使用。数据持久化意味着将内存中的数据保存到硬盘上加以固化。
- 持久化的主要应用式将内存中的数据存储在关系型的数据库中，当然也可以存储在一般磁盘文件、XML数据文件中。

## Java中的数据存储技术

- 在Java中，数据库存取技术可分为两类：
     + JDBC直接访问数据库
     + 第三方O/R工具，如Hibernate、ibatis等
- *JDBC是Java访问数据库的基石，其它技术都是对JDBC的封装*


## JDBC

### JDBC基础

- JDBC（Java Database Connectivity）是一个独立于特定数据库的管理系统、通用的SQL数据库存取和操作的公共接口（一组API）。它定义了用来访问数据库的标准Java类库，使用这个类库可以以一种标准的方法方便地访问数据库资源。
- JDBC为访问不同的数据库提供了一种统一的途径，为开发者屏蔽了一些细节问题。
- JDBC的目标是使Java程序员使用JDBC可以链接任何提供了JDBC驱动程序的数据库系统。

**JDBC本身是Java链接数据库的一个标准，是进行数据库链接的抽象层，由Java编写的一组类类和接口组成，接口的实现由各个数据库厂商来完成。**

要链接不同的数据库需要使用不同的驱动包（.jar）。

### ODBC
ODBC（Open Database Connectivity），开放数据库链接。这是微软开放服务结构中有关数据库的一个组成部分，它同样建立了一组规范并提供了一组对数据库访问的标准API。

### JDBC分类

目前有四种可供使用的JDBC驱动程序，不同类型的驱动程序有着不一样的使用方法。

- JDBC-ODBC桥：桥接型驱动程序，这类驱动程序要求必须在使用者端的计算机上事先安装好ODBC驱动，然后通过JDBC-ODBC的调用方法进而通过ODBC类存取数据。
- 本地API驱动：这种类型的JDBC驱动程序使用Java编写，它调用数据库厂商提供的本地API。通过这种类型JDBC驱动程序访问数据库减少了ODBC的调用环节，提高了数据库的访问效率。在这种方式下需要在客户的机器上安装本地JDBC驱动程序和特定厂商的本地API。
- 网络协议驱动：这种驱动利用中间件的应用服务器来访问数据库。应用服务器作为一个到多个数据库的网关，客户端通过它可以链接到不同的数据库服务器。应用服务器通常有自己的网络协议，Java用户程序通过JDBC程序驱动将JDBC调用发送给应用服务器，应用服务器使用本地程序驱动访问数据库，从而完成请求。
- 本地协议驱动：这类驱动程序是最成熟的JDBC驱动程序，无需额外的驱动程序也无需在服务端安装任何中间件，所有存取数据库的操作都直接由驱动程序来完成。多数数据库厂商已经支持允许客户程序通过网络直接与数据库通信的网络协议。这种类型的驱动程序完全由Java编写，通过与数据库建立Socket链接，采用具体与厂商的网络协议把JDBC调用转换为直接链接的网络调用。

JDBC-ODBC桥：
访问路径： JDBC API ----> JDBC-ODBC桥 ----> ODBC API ----> ODBC层 ----> 数据库
该方法性能不高。

本地API驱动：
访问路径：Java应用程序 ----> JDBC API ----> JDBC驱动程序 ----> 厂商提供的本地API  ----> 数据库

网络协议驱动：
访问路径：Java应用程序 ----> JDBC API ----> JDBC驱动程序 ----> 应用服务器 ----> 厂商提供的本地API ----> 数据库

本地协议驱动：
访问路径：Java应用程序 ----> JDBC API ----> JDBC驱动程序 ----> 数据库

### 链接至数据库

#### 使用Driver接口
- Java.sql.Driver接口是所有JDBC驱动程序需要实现的接口。不同厂商提供不同的实现。
- 在程序中不需要直接取去访问实现了Driver接口的类，而是由驱动程序管理器类（Java.sql.FriverManager）去调用这些Driver实现。

使用Driver类下的connect方法进行连接。

    Connection connect(String url, Properties info) throws SQLException
>
Attempts to make a database connection to the given URL. The driver should return "null" if it realizes it is the wrong kind of driver to connect to the given URL. This will be common, as when the JDBC driver manager is asked to connect to a given URL it passes the URL to each loaded driver in turn. 
The driver should throw an SQLException if it is the right driver to connect to the given URL but has trouble connecting to the database. 
>
The Properties argument can be used to pass arbitrary string tag/value pairs as connection arguments. Normally at least "user" and "password" properties should be included in the Properties object. 

>
**Parameters:**
url - the URL of the database to which to connect 
info - a list of arbitrary string tag/value pairs as connection arguments. Normally at least a "user" and "password" property should be included.
**Returns:**
a Connection object that represents a connection to the URL 
**Throws:** 
SQLException - if a database access error occurs or the url is null 

参数url的写法：
The JDBC URL format for MySQL Connector/J is as follows, with items in square brackets ([, ]) being optional:

    jdbc:mysql://[host][,failoverhost...][:port]/[database] »
    [?propertyName1][=propertyValue1][&propertyName2][=propertyValue2]...

If the hostname is not specified, it defaults to 127.0.0.1. If the port is not specified, it defaults to 3306,the default port number for MySQL servers.

    jdbc:mysql://[host:port],[host:port].../[database] »
    [?propertyName1][=propertyValue1][&propertyName2][=propertyValue2]...

示例：

    Driver driver = new com.mysql.jdbc.Driver();
    Properties info = new Properties();
    info.setProperty("user", "root");
    info.setProperty("password", "mysql");
    String url = "jdbc:mysql://localhost:3306/test";
    Connection conn = driver.connect(url, info);

#### 使用DriverManager类

使用DriverManager类下的registerDriver()方法对驱动程序进行注册。

    static void registerDriver(Driver driver)  

>
throws SQLExceptionRegisters the given driver with the DriverManager. A newly-loaded driver class should call the method registerDriver to make itself known to the DriverManager. If the driver is currently registered, no action is taken.
>
**Parameters:**
driver - the new JDBC Driver that is to be registered with the DriverManager 
**Throws:**
SQLException - if a database access error occurs 
NullPointerException - if driver is null 

注册后使用getConnection()方法获取建立到给定数据库的连接。

    static Connection getConnection(String url, String user, String password)  
>
Attempts to establish a connection to the given database URL. The DriverManager attempts to select an appropriate driver from the set of registered JDBC drivers. 
>
**Parameters:**
url - a database url of the form jdbc:subprotocol:subname 
user - the database user on whose behalf the connection is being made 
password - the user's password 
**Returns:** 
a connection to the URL 
**Throws:**
SQLException - if a database access error occurs or the url is null 
SQLTimeoutException - when the driver has determined that the timeout value specified by the setLoginTimeout method has been exceeded and has at least tried to cancel the current database connection attempt 

示例：

    // 注册驱动
    DriverManager.registerDriver(new com.mysql.jdbc.Driver());
    // 设置要链接数据库的url
    String url = "jdbc:mysql://localhost:3306/test";
    // 获取连接
    Connection conn = DriverManager.getConnection(url, "root", "mysql");
    System.out.println("conn: " + conn);
    // output: conn: com.mysql.jdbc.JDBC4Connection@5a10411

#### 使用Class.forName()加载驱动

>实际开发中使用的方式。

- 使用Class.forName(String classPath)加载驱动（字节码文件），向其传递要加载的JDBC驱动的类名。
- 这里不用调用DriverManager.registerDriver()来注册驱动程序类的实例。加载字节码文件会调用类中的静态代码块（但不会调用类的构造方法，即不会创建实例），静态代码块中包含了对驱动进行注册的语句。

示例：

    // 设置要链接数据库的url
    String url = "jdbc:mysql://localhost:3306/test";

    try {
    
        // 加载驱动的字节码文件（String classPath），并注册驱动。
        Class.forName("com.mysql.jdbc.Driver");
        
        // 获取连接
        Connection conn = DriverManager.getConnection(url, "root", "mysql");
        System.out.println("conn: " + conn);
        
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    } catch (SQLException e) {
        e.printStackTrace();
    }

### 对数据库进行操作

**对数据库进行增删改查通常把一行数据封装为一个javabean对象。**

#### DML & DDL语句

    package bit.mysql.connection.statement;

    import java.sql.Connection;
    import java.sql.DriverManager;
    import java.sql.SQLException;
    import java.sql.Statement;

    public class TestStatement {

        public static void main(String[] args) {

            /*
             * 1、注册驱动
             */
            try {
                Class.forName("com.mysql.jdbc.Driver");
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }

            /*
             * 2、获取连接
             */
            Connection conn = null;
            try {
                String url = "jdbc:mysql://localhost:3306/test";
                conn = DriverManager.getConnection(url, "root", "mysql");
                System.out.println("Succeed in connecting " + conn);
            } catch (SQLException e) {
                e.printStackTrace();
            }

            /*
             * 3、创建Statement对象
             * 使用Connection类的方法：
             * Statement createStatement() throws SQLException
             * 建立一个Statement对象来将SQL语句发送到数据库
             */
            Statement stmt = null;
            try {
                stmt = conn.createStatement();
            } catch (SQLException e) {
                e.printStackTrace();
            }

            /*
             * 4、组织并执行sql语句
             * 使用Statement类的方法：
             * int executeUpdate(String sql) throws SQLException
             * 执行给定的sql语句，该语句可能是DML(INSERT、UPDATE、DELETE)语句，返回影响的行数。
             * 或是DDL(CREATE、DROP、ALTER)语句，返回0。
             */
            try {
                // DML语句
                String sqlInsert = "INSERT INTO test1(id, NAME, gender) VALUES(NULL, '李', '女')";
                String sqlUpdate = "UPDATE test1 SET NAME='张', gender='男' WHERE gender='女'";
                String sqlDelete = "DELETE FROM test1 WHERE NAME='A'";
                
                // DDL语句
                String sqlCreate = "CREATE TABLE test2(id INT,NAME VARCHAR(20))";
                // 执行三次插入
                for (int i = 0; i < 3; i++) {
                    int k = stmt.executeUpdate(sqlInsert);
                    System.out.println("Insert : " + k); 
                }
                
                // 执行一次更新
                int l = stmt.executeUpdate(sqlUpdate);
                System.out.println("Update: " + l);
                
                // 执行一次删除
                int m = stmt.executeUpdate(sqlDelete);
                System.out.println("Delete: " + m);
                
                // 执行一次创建
                int o = stmt.executeUpdate(sqlCreate);
                System.out.println("Create: " + o);
            } catch (SQLException e) {
                e.printStackTrace();
            }

            /*
             * 5、关闭资源 
             * 先关闭Statement，再关闭连接。
             */
            try {
                if (stmt != null) {
                    // 立即释放此Statement对象的数据库和JDBC资源
                    stmt.close();
                }
                if (conn != null) {
                    // 立即释放此Connection对象的数据库和JDBC资源
                    conn.close();
                }
            } catch (SQLException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
    }

#### SELECT语句

使用executeQuery方法执行SELECT语句。ResultSet表示数据库结果集的数据表。

    ResultSet executeQuery(String sql) throws SQLException
>
**Note:This method cannot be called on a PreparedStatement or CallableStatement.**
>
**Parameters:** 
sql - an SQL statement to be sent to the database, typically a static SQL SELECT statement 
**Returns:** 
a ResultSet object that contains the data produced by the given query; **never null** 
**Throws:**
SQLException - if a database access error occurs, this method is called on a closed Statement, the given SQL statement produces anything other than a single ResultSet object, the method is called on a PreparedStatement or CallableStatement 
SQLTimeoutException - when the driver has determined that the timeout value that was specified by the setQueryTimeout method has been exceeded and has at least attempted to cancel the currently running Statement 

使用ResultSet类的next()方法调整结果数据表的指针。

    boolean next()throws SQLExceptionMoves 
>
**the cursor forward one row from its current position**. A ResultSet cursor is initially positioned before the first row; the first call to the method next makes the first row the current row; the second call makes the second row the current row, and so on. 
When a call to the next method returns false, the cursor is positioned after the last row. Any invocation of a ResultSet method which requires a current row will result in a SQLException being thrown. If the result set type is **TYPE_FORWARD_ONLY**, it is vendor specified whether their JDBC driver implementation will return false or throw an SQLException on a subsequent call to next. 
>
If an input stream is open for the current row, a call to the method next will implicitly close it. A ResultSet object's warning chain is cleared when a new row is read.
>
**Returns:** 
true if the new current row is valid; false if there are no more rows 
**Throws:**
SQLException - if a database access error occurs or this method is called on a closed result set 

取出结果集中值的方法：

1、使用getXxxx(String columnLable)方法返回表中的数据，其中Xxxx要与columnLable所代表的数据类型相对应。
**如果sql查询语句中使用定义了列的别名，则在获取数据时要使用别名**。
示例：
    
    select employee_id id from employees
此时结果集中不存在employee_id列，而存在id列，使用getString("id")。

MySql数据类型和Java数据类型对应表

类型名称 | 显示长度 | 数据库类型 | JAVA类型 | JDBC类型索引(int) 
------|------|------|------|------  
VARCHAR | L+N | VARCHAR | java.lang.String | 12  
CHAR | N | CHAR | java.lang.String | 1   
BLOB | L+N | BLOB | java.lang.byte[] | -4  
TEXT | 65535 | VARCHAR | java.lang.String | -1  
INTEGER | 4 | INTEGER UNSIGNED | java.lang.Long | 4   
TINYINT | 3 | TINYINT UNSIGNED | java.lang.Integer | -6  
SMALLINT | 5 | SMALLINT UNSIGNED | java.lang.Integer | 5   
MEDIUMINT | 8 | MEDIUMINT UNSIGNED | java.lang.Integer | 4   
BIT | 1 | BIT | java.lang.Boolean | -7  
BIGINT | 20 | BIGINT UNSIGNED | java.math.BigInteger | -5  
FLOAT | 4+8 | FLOAT | java.lang.Float | 7   
DOUBLE | 22 | DOUBLE | java.lang.Double | 8   
DECIMAL | 11 | DECIMAL | java.math.BigDecimal | 3   
BOOLEAN | 1 | TINYINT UNSIGNED | java.lang.Integer | -6  
ID | 11 | PK (INTEGER UNSIGNED) | java.lang.Long | 4   
DATE | 10 | DATE | ava.sql.Date | 91  
TIME | 8 | TIME | java.sql.Time | 92  
DATETIME | 19 | DATETIME | java.sql.Timestamp | 93  
TIMESTAMP | 19 | TIMESTAMP | java.sql.Timestamp | 93  
YEAR | 4 | YEAR | java.sql.Date | 91

2、使用getXxxx(int columnIndex)
其中columnIndex为列的索引。第一列为1，第二列为2 ...

3、使用getObject(String columnLable)、getObject(int columnIndex)

### PreparedStatement 接口

    public interface PreparedStatement extends statement

#### 基本数据库操作

- 可以通过调用Connection对象的preparedStatement()方法获得PrepareStatement对象。
- PreparedStatement接口是Statement的子接口，它表示一条预编译的sql语句。
- PreparedStatement对象所代表的sql语句中的参数用(?)来表示，调用PreparedStatement对象的setXxxx()方法来设置这些参数。setXxxx()方法有两个参数，第一个参数是要设置的sql语句中参数的索引（该索引从1开始），第二个是设置的sql语句中参数的值。

>
An object that represents a precompiled SQL statement. 
A SQL statement is precompiled and stored in a PreparedStatement object. This object can then be used to efficiently execute this statement multiple times. 
>
Note: The setter methods (setShort, setString, and so on) for setting IN parameter values must specify types that are compatible with the defined SQL type of the input parameter. For instance, if the IN parameter has SQL type INTEGER, then the method setInt should be used. 
>
If arbitrary parameter type conversions are required, the method setObject should be used with a target SQL type. 
>
In the following example of setting a parameter, con represents an active connection: 

    PreparedStatement pstmt = con.prepareStatement("UPDATE EMPLOYEES SET SALARY = ? WHERE ID = ?");
    pstmt.setBigDecimal(1, 153833.00)
    pstmt.setInt(2, 110592)

PreparedStatement对象使用executeUpdate()执行sql（DDL、DML）语句。

    int executeUpdate() throws SQLExceptionExecutes 

>
the SQL statement in this PreparedStatement object, which must be an SQL Data Manipulation Language (DML) statement, such as INSERT, UPDATE or DELETE; or an SQL statement that returns nothing, such as a DDL statement.
**Returns:** 
either (1) the row count for SQL Data Manipulation Language (DML) statements or (2) 0 for SQL statements that return nothing 
**Throws:** 
SQLException - if a database access error occurs; this method is called on a closed PreparedStatement or the SQL statement returns a ResultSet object 
SQLTimeoutException - when the driver has determined that the timeout value that was specified by the setQueryTimeout method has been exceeded and has at least attempted to cancel the currently running Statement 

PreparedStatement对象使用executeQuery()执行select语句。

    ResultSet executeQuery() throws SQLExceptionExecutes 

>
the SQL query in this PreparedStatement object and returns the ResultSet object generated by the query.
**Returns:**
a ResultSet object that contains the data produced by the query; never null 
**Throws:** 
SQLException - if a database access error occurs; this method is called on a closed PreparedStatement or the SQL statement does not return a ResultSet object 
SQLTimeoutException - when the driver has determined that the timeout value that was specified by the setQueryTimeout method has been exceeded and has at least attempted to cancel the currently running Statement 

#### PreparedStatement与Statement比较

- PreparedStatement的代码可读性较高但可维护性较低。
- PreparedStatement能最大可能的提高性能能：
    + DBServer会对预编译语句提供性能优化。因为预编译语句有可能会被重复调用，所以被DBServer的编译器编译后的执行代码会缓存下来，下次调用时只要是相同的预编译语句就不再需要编译，只要将参数直接传入编译后的执行代码就可以执行。
    + 在Statement语句中，即使是相同操作但因为数据内容不一样也要重新进行编译。事实上没有数据库会对普通语句编译后的执行代码进行缓存，而是每执行一次都要重新编译一次。
- PreparedStatement能保证安全性（防止sql注入）。


