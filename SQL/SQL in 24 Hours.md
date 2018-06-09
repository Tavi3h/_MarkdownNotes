[TOC]

# SQL in 24 Hours - - - SQL入门经典

*使用MySQL 5.7.19实现，搭配SQLyog图形化工具。*

## 第二部分 建立数据库

### 第2章 定义数据结构

#### 2.1 基本数据类型

##### 2.1.1 定长字符串

定长字符串通常具有相同的长度，是使用定长数据类型保存的。

**SQL定长字符串标准：CHARACTER(n)，n定义了该字段里能保存的最多字符数量。**

*某些SQL使用CHAR数据类型来保存定长数据。*

在定长数据类型里，通常使用空格来填充数量不足的字符。如果字段长度是10，而输入的数据只有5位，那么剩余5位将被记录为空格。填充空格确保了字段里每个值都具有相同的长度。

**不要使用定长数据类型来保存长度不定的数据。不恰当地使用定长数据类型回导致空间的浪费。**

##### 2.1.2 变长字符串

**SQL变长字符串的标准：CHARACTER VARYING(n)，n定义了字段里能够保存的最多的字符数量。**

常见的变长字符串数据类型有VARCHAR、VARINARY和VARCHAR2。
*VARINARY、VARCHAR2由Oracle使用。*

**变长字符串不会使用空格来填充字段中的空白。**

##### 2.1.3 大对象类型

- BLOB：二进制大对象，它的数据是很长的二进制字符串。BLOB适合在数据库里存储二进制媒体文件，比如图像和MP3。
- TEXT：长字符串类型，可以被看作一个更大的VARCHAR字段，通常用于在数据库里保存大字符集。

##### 2.1.4 数值类型

数值保存在定义为某种数值类型的字段里，一般包括NUMBER、INTEGER、REAL、DECIMAL等。

**SQL数值的标准：**p表示字段最大长度、s表示小数点后面的位数

- BIT(n)
- BIT VARYING(n)
- DECIMAL(p,s)
- INTEGER
- SMALLINT
- BIGINT
- FLOAT(p,s)
- DOUBLE PRECISION(p,s)
- REAL(s)

##### 2.1.5 日期和时间类型

标准SQL支持DATETIME数据类型，包含：

- DATE
- TIME
- DATETIME
- TIMESTAMP

DATETIME数据类型的元素包括：

- YEAR
- MONTH
- DAY
- HOUR
- MINUTE
- SECOND

日期一般不指定长度。

##### 2.1.6 直义字符串

直义字符串就是一些列字符，这是由用户或程序明确指定的。直义字符串包含的数据与前面介绍的数据类型具有一样的属性，但是字符串的值是已知的。

范围如下所示：

- 'Hello'
- 45000
- "45000"
- 3.14
- 'November 1, 1997'

通常来说字符型的字符串由单引号包围，数值45000没有用单引号包围，而第二个45000用双引号报告为了。一般来说，字符型字符串需要使用单引号，而数值型不需要。

*如果一个数据没有用单引号包围，SQL程序就会将其认定为数值类型。*

##### 2.1.7 NULL数据类型

NULL值表示没有值。

在使用NULL数据类型时，需要明确它表示相应字段不是必须要输入数据的。如果某个字段必须包含数据，就把它设置为NOT NULL。只要字段有可能不包含数据，最好就把它设置为NULL。

##### 2.1.8 布尔值

布尔值的取值范围是TRUE、FALSE和NULL，用于进行数据比较。

大多数数据库实现并没有一个严格意义上的BOOLEAN类型，而是代之以各自不同的实现方法。

- MySQL拥有BOOLEAN类型，但实质上与TINYINT类型相同。
- Oracle倾向于让用户使用CHAR(1)来代替布尔值。
- SQL Server则使用BIT来代替。

##### 2.1.9 自定义类型（MySQL不支持）

使用语句CREATE TYPE用于创建自定义类型。

    CREATE TYPE PERSON AS OBJECT
    (
        NAME VARCHAR(20),
        SSN VARCHAR(9)
    );

然后可以这样引用：

    CREATE TABLE EMP_PAY
    (
        EMPLOYEE PERSON,
        SALARY DECIMAL(10,2),
        HIRE_RATE DATE
    );

##### 2.1.10 域（MySQL不支持）

域是能够被使用的有效数据类型的集合。域与数据相关联，从而只接受特定的数据。在域创建之后，我们可以向域添加约束。约束与数据类型共同发挥作用，从而进一步限制字段能够接受的数据。域的使用类似于自定义类型。

创建域：

    CREATE DOMAIN MONEY_D AS NUMBER(8,2);

为域添加约束：

    ALTER DOMAIN MONEY_D 
    ADD CONSTRAINT MONEY_CON1
    CHECK (VALUE > 5);

引用域：

    CREATE TABLE EMP_PAY
    (
        EMP_ID NUMBER(9),
        EMP_NAME VARCHAR2(30),
        PAY_RATE MONEY_D
    );

##### 2.1.11 MySQL中的常用字段类型

数据类型 | 说明 
------|------
CHAR(n) | 定长字符，0-255字节
VARCHAR(n) | 变长字符，0-255字节
DATE | 日期数据，使用‘YYYY-MM-DD’格式进行检索和显示date值
DATETIME | 日期数据，比date更精确，包含时分秒，格式‘YYYY-MM-DD HH:MM:SS’
INT | 整型数据
DOUBLE(p,s) | 数字型，可存放实型和整型，p：范围 s：精度
BLOB | 存放图形、声音和影像，二进制对象，0-65535字节
TEXT | 存放大文本文件，0-65535字节

##### 2.1.12 MySQL支持的所有原生数据类型

**数值类型**

MySQL支持所有标准SQL数值数据类型。
这些类型包括严格数值数据类型(INTEGER、SMALLINT、DECIMAL和NUMERIC)，以及近似数值数据类型(FLOAT、REAL和DOUBLE PRECISION)。
关键字INT是INTEGER的同义词，关键字DEC是DECIMAL的同义词。
BIT数据类型保存位字段值，并且支持MyISAM、MEMORY、InnoDB和BDB表。
作为SQL标准的扩展，MySQL也支持整数类型TINYINT、MEDIUMINT和BIGINT。下面的表显示了需要的每个整数类型的存储和范围。

<table>
    <tr>
        <th width="10%">类型</th>
        <th width="15%">大小</th>
        <th width="30%">范围（有符号）</th>
        <th width="30%">范围（无符号）</th>
        <th width="15%">用途</th>
    </tr>
    <tr>
        <td>TINYINT</td>
        <td>1 字节</td>
        <td>(-128，127)</td>
        <td>(0，255)</td>
        <td>小整数值</td>
    </tr>
    <tr>
        <td>SMALLINT</td>
        <td>2 字节</td>
        <td>(-32 768，32 767)</td>
        <td>(0，65 535)</td>
        <td>大整数值</td>
    </tr>
    <tr>
        <td>MEDIUMINT</td>
        <td> 3 字节</td>
        <td>(-8 388 608，8 388 607)</td>
        <td>(0，16 777 215)</td>
        <td>大整数值</td>
    </tr>
    <tr>
        <td>INT或INTEGER</td>
        <td>4 字节</td>
        <td>(-2 147 483 648，2 147 483 647)</td>
        <td>(0，4 294 967 295)</td>
        <td>大整数值</td>
    </tr>
    <tr>
        <td>BIGINT</td>
        <td>8 字节</td>
        <td>(-9 233 372 036 854 775 808，9 223 372 036 854 775 807)</td>
        <td>(0，18 446 744 073 709 551 615)</td>
        <td>极大整数值</td>
    </tr>
    <tr>
        <td>FLOAT</td>
        <td>4 字节</td>
        <td>(-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38)</td>
        <td>0，(1.175 494 351 E-38，3.402 823 466 E+38)</td>
        <td>单精度<br>浮点数值</td>
    </tr>
    <tr>
        <td>DOUBLE</td>
        <td>8 字节</td>
        <td>(-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693134 862 315 7 E+308)</td>
        <td>0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308)</td>
        <td>双精度<br>浮点数值</td>
    </tr>
    <tr>
        <td>DECIMAL</td>
        <td>对DECIMAL(M,D) ，如果M&gt;D，为M+2否则为D+2</td>
        <td>依赖于M和D的值</td>
        <td>依赖于M和D的值</td>
        <td>小数值</td>
    </tr>
</table>

**日期和时间类型**

表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。
每个时间类型有一个有效值范围和一个"零"值，当指定不合法的MySQL不能表示的值时使用"零"值。

<table>
    <tr>
        <th width="10%">类型</th>
        <th width="10%">大小<br>(字节)</th>
        <th width="40%">范围</th>
        <th width="20%">格式</th>
        <th>用途</th>
    </tr>
    <tr>
        <td width="10%">DATE</td>
        <td width="10%">3</td>
        <td>1000-01-01/9999-12-31</td>
        <td>YYYY-MM-DD</td>
        <td>日期值</td>
    </tr>
    <tr>
        <td width="10%">TIME</td>
        <td width="10%">3</td>
        <td>'-838:59:59'/'838:59:59'</td>
        <td>HH:MM:SS</td>
        <td>时间值或持续时间</td>
    </tr>
    <tr>
        <td width="10%">YEAR</td>
        <td width="10%">1</td>
        <td>1901/2155</td>
        <td>YYYY</td>
        <td>年份值</td>
    </tr>
    <tr>
        <td width="10%">DATETIME</td>
        <td width="10%">8</td>
        <td width="40%">1000-01-01 00:00:00/9999-12-31 23:59:59</td>
        <td>YYYY-MM-DD HH:MM:SS</td>
        <td>混合日期和时间值</td>
    </tr>
    <tr>
        <td width="10%">TIMESTAMP</td>
        <td width="10%">4</td>
        <td width="40%"><p>1970-01-01 00:00:00/2038 </p><p>结束时间是第 <strong>2147483647</strong> 秒，北京时间 <strong>2038-1-19 11:14:07</strong>，格林尼治时间2038年1月19日凌晨03:14:07</p></td>
        <td>YYYYMMDD HHMMSS</td>
        <td>混合日期和时间值，时间戳</td>
    </tr>
</table>

**字符串类型**

字符串类型指CHAR、VARCHAR、BINARY、VARBINARY、BLOB、TEXT、ENUM和SET。该节描述了这些类型如何工作以及如何在查询中使用这些类型。

<table>
        <tr>
            <th width="20%">类型</th>
            <th width="25%">大小</th>
            <th width="55%">用途</th>
        </tr>
        <tr>
            <td>CHAR</td>
            <td>0-255字节</td>
            <td>定长字符串</td>
        </tr>
        <tr>
            <td>VARCHAR</td>
            <td>0-65535 字节</td>
            <td>变长字符串</td>
        </tr>
        <tr>
            <td>TINYBLOB</td>
            <td>0-255字节</td>
            <td>不超过 255 个字符的二进制字符串</td>
        </tr>
        <tr>
            <td>TINYTEXT</td>
            <td>0-255字节</td>
            <td>短文本字符串</td>
        </tr>
        <tr>
            <td>BLOB</td>
            <td>0-65 535字节</td>
            <td>二进制形式的长文本数据</td>
        </tr>
        <tr>
            <td>TEXT</td>
            <td>0-65 535字节</td>
            <td>长文本数据</td>
        </tr>
        <tr>
            <td>MEDIUMBLOB</td>
            <td>0-16 777 215字节</td>
            <td>二进制形式的中等长度文本数据</td>
        </tr>
        <tr>
            <td>MEDIUMTEXT</td>
            <td>0-16 777 215字节</td>
            <td>中等长度文本数据</td>
        </tr>
        <tr>
            <td>LONGBLOB</td>
            <td>0-4 294 967 295字节</td>
            <td>二进制形式的极大文本数据</td>
        </tr>
        <tr>
            <td>LONGTEXT</td>
            <td>0-4 294 967 295字节</td>
            <td>极大文本数据</td>
        </tr>
</table>

### 第3章 管理数据库对象

#### 3.1 什么是数据库对象

数据库对象是数据库中定义的、用于存储或引用数据的对象，比如表、视图、簇、序列、索引和异名。

#### 3.2 什么是规划

规划是与数据库某个用户名相关联的数据库对象集合。相应的用户名被称为规划所有人，或是关联对象组的所有人。

数据库里可以有一个或多个规划，用户只与同名规划相关联。

一般来说，当用户创建一个对象时，就是在自己的规划里创建了它，除非明确指定在另一个规划里创建它。因此，根据在数据库里的权限，用户可以创建、操作和删除对象。

规划可以只包含一个表，也可以包含无数个对象，其上限由具体的SQL实现决定。

假设我们从管理员获得了一个数据库用户名和密码，用户名是USER1。我们登陆到数据库并创建了一个名为EMPLOYEE_TBL的表，这时对于数据库来说，表的实际名称是USER1.EMPLOYEE_TBL，这个表的规划名是USER1，也就是这个表的所有者。

当我们访问自己所拥有的表时不必引用规划名称，以下两种方式都可以引用刚才创建的表：

    EMPLOYEE_TBL
    USER1.EMPLOYEE_TBL

但是如果其他用户要访问这个表，就必须指定规划名称：

    USER1.EMPLOYEE_TBL

#### 3.3 表：数据的主要存储方式

表是关系型数据库中最主要的数据存储对象，其最简单形式是由行和列组成，分别都包含着数据。表在数据库中占据实际的物理空间，可以是永久的或是临时的。

##### 3.3.1 列

字段在关系型数据库中也称为列，他是表的组成部分，被设置为特定的数据类型。

列可以指定为NULL或NOT NULL，当设置为NOT NULL时，表示其中必须包含数据；设置为NULL时，就表示可以不包含数据。

##### 3.3.2 行

行是数据库表里的一条记录。

##### 3.3.3 CREATE TABLE语句

CREATE TABLE语句用于创建表。**虽然创建表的实际操作十分简单，但在执行该命令前应该花更多的时间和精力来设计表的结构。**

创建表的基本语法：

    CREATE TABLE table_name
    (
        field1 data_type constraint_definition,
        field2 data_type constraint_definition,
        field3 data_type constraint_definition,
        field4 data_type constraint_definition,
        ...
        fieldn data_type constraint_definition
    );

示例，创建一个名为employee_tbl的表：

    CREATE TABLE employee_tbl
    (
        EMP_ID CHAR(9) NOT NULL,
        EMP_NAME VARCHAR(40) NOT NULL,
        EMP_ST_ADDR VARCHAR(20) NOT NULL,
        EMP_CITY VARCHAR(15) NOT NULL,
        EMP_ST CHAR(2) NOT NULL,
        EMP_ZIP INTEGER(5) NOT NULL,
        EMP_PHONE INTEGER(10) NULL,
        EMP_PAGER INTEGER(10) NULL
    );

*列的默认属性是NULL，所以在CREATE TABLE语句中不必明确设置，但NOT NULL必须明确指定。*

##### 3.3.4 命名规范

在为对象选择名称时，特别是表和列的名称，应该让名称反应出所保存的数据。

##### 3.3.5 ALTER TABLE命令

在表被创建之后，我们可以使用ALTER TABLE命令对其进行修改。可以添加列、删除列、修改列定义、添加和去除约束。

ALTER TABLE命令标准如下：

    ALTER TABLE table_name [modify] [column_name] [datatype|null not null]
    [restrict|cascade]
    [drop] [constraint constraint_name]
    [add] [column] column definition;

**
修改表的元素：
**

列的属性是其所包含数据的规则和行为。使用ALTER TABLE命令可以修改列的属性，这里属性的含义是：

- 列的数据类型
- 列的长度、有效位或标度
- 列值能否为空

示例，修改employee_tbl的EMP_ID列：

    ALTER TABLE employee_tbl MODIFY
    EMP_ID VARCHAR(10);

执行后该列数据类型没有变，但是长度从9变为10.

**
添加列：
**

示例：向表_studysql.employee_tbl 添加一列数据类型为REAL名为test的列，该列不为NULL，插入位置在列POSITION后。

    ALTER TABLE `_studysql`.`employee_tbl` 
    ADD COLUMN `test` REAL NOT NULL AFTER `POSITION`;

插入属性为NOT NULL的列时，如果原表中有数据，则该列自动获得默认值。例如对于REAL类型的列，该列会被赋值为0。

**
添加自动增加的列：
**

在MySQL中创建自动增加的列可以将列设置为AUTO_INCREMENT或者SERIAL。

将列设置为SERIAL等价于将以下属性设置给该列：

- BIGINT(20) 
- UNSIGNED 
- NOT NULL 
- AUTO_INCREMENT 
- UNIQUE

在SQL Server中可以使用IDENTITY。Oracle没有提供直接的方法来创建自动增加的列，但可以使用SQUENCE对象和一个触发器来实现类似的效果。

**
修改列：
**

下面是修改列的一些通用规则：

- 列的长度可以增加到特定数据类型所允许的最大长度；
- 如果想缩短某列的长度，则必须要求这一列在表里所有数据的长度都小于或等于新长度；
- 数值数据的位数可以增加；
- 如果要缩短数值数据的位数，则必须要求这一列在表里所有数值的位数小于或等于新指定的位数；
- 数值里的小数位数可以增加或减少；
- 列的数据类型一般是可以改变的。

**
删除列：
**

删除列使用DROP命令：

    ALTER TABLE `employee_tbl` DROP COLUMN `test`;

此时会删除表employee_tbl中的test列。

##### 3.3.6 从现有表新建另一个表

利用CREATE TABLE语句和SELECT语句的组合可以复制现有的表。新表具有同样的列定义，我们可以选择任何列或全部列。由函数或多列组合创建出来的列会自动保持数据所需大小。

语法如下：

    create table new_table_name as
    select [*|column1,column2,...]
    from table_name
    [where]

MySQL和Oracle都支持使用CREATE TABLE AS SELECT方法，在一个表的基础上创建另一个表。但是SQL Server不同，它使用SELECT ... INTO方法。语法如下：

    select [*|column1,column2,...]
    into new_table_name
    from table_name
    [where]

从现有表创建新表，新表与原始表具有一样的属性。

##### 3.3.7 删除表

删除表是一种相当简单的操作。如果使用了RESTRICT选项，并且表被视图或约束所引用，DROP语句就会返回一个错误。当使用了CASCADE选项时，删除操作会成功执行，而且全部引用视图和约束都被删除。删除表的语法如下：

    drop table table_name [restrict|cascade]

*SQL Server中不能使用cascade选项。*

**
删除表时，在提交命令之前要确保指定了表的规划名或所有者，否则可能会误删其他表。如果使用多用户账户，在删除表之前一定要确定使用了适当的用户名连接数据库。
**

#### 3.4 完整性约束

完整性约束用于确定关系型数据库里数据的准确性和一致性。在关系型数据库里，数据完整性是通过引用完整性的概念实现的，而在引用完整性里包含了很多类型。

##### 3.4.1 主键约束

主键是表里一个或多个用于实现记录唯一性的字段。通常主键由一个字段构成，但也可以由多个字段组成。主键的作用在于表里每条记录都具有唯一的值。

示例：

    CREATE TABLE employee_tbl
    (
        EMP_ID CHAR(9) NOT NULL PRIMARY KEY,
        EMP_NAME VARCHAR(40) NOT NULL,
        EMP_ST_ADDR VARCHAR(20) NOT NULL,
        EMP_CITY VARCHAR(15) NOT NULL,
        EMP_ST CHAR(2) NOT NULL,
        EMP_ZIP INTEGER(5) NOT NULL,
        EMP_PHONE INTEGER(10) NULL,
        EMP_PAGER INTEGER(10) NULL
    );

也可以这样定义：

    CREATE TABLE employee_tbl
    (
        EMP_ID CHAR(9) NOT NULL,
        EMP_NAME VARCHAR(40) NOT NULL,
        EMP_ST_ADDR VARCHAR(20) NOT NULL,
        EMP_CITY VARCHAR(15) NOT NULL,
        EMP_ST CHAR(2) NOT NULL,
        EMP_ZIP INTEGER(5) NOT NULL,
        EMP_PHONE INTEGER(10) NULL,
        EMP_PAGER INTEGER(10) NULL,
        PRIMARY KEY(EMP_ID)
    );

也可以使用ALTER命令增加主键约束：

    CREATE TABLE employee_tbl
    (
        EMP_ID CHAR(9) NOT NULL,
        EMP_NAME VARCHAR(40) NOT NULL,
        EMP_ST_ADDR VARCHAR(20) NOT NULL,
        EMP_CITY VARCHAR(15) NOT NULL,
        EMP_ST CHAR(2) NOT NULL,
        EMP_ZIP INTEGER(5) NOT NULL,
        EMP_PHONE INTEGER(10) NULL,
        EMP_PAGER INTEGER(10) NULL
    );
    ALTER TABLE employee_tbl ADD
    CONSTRAINT PK_EMP_ID PRIMARY KEY(EMP_ID); -- PK_EMP_ID为约束名，下同

##### 3.4.2 唯一性约束

唯一性约束要求表里某个字段的值在每条记录里都是唯一的，这一点与主键类似。

示例：

    CREATE TABLE employee_tbl
    (
        EMP_ID CHAR(9) NOT NULL PRIMARY KEY,
        EMP_NAME VARCHAR(40) NOT NULL,
        EMP_ST_ADDR VARCHAR(20) NOT NULL,
        EMP_CITY VARCHAR(15) NOT NULL,
        EMP_ST CHAR(2) NOT NULL,
        EMP_ZIP INTEGER(5) NOT NULL,
        EMP_PHONE INTEGER(10) NULL UNIQUE,
        EMP_PAGER INTEGER(10) NULL
    );

也可以这样定义：

    CREATE TABLE employee_tbl
    (
        EMP_ID CHAR(9) NOT NULL,
        EMP_NAME VARCHAR(40) NOT NULL,
        EMP_ST_ADDR VARCHAR(20) NOT NULL,
        EMP_CITY VARCHAR(15) NOT NULL,
        EMP_ST CHAR(2) NOT NULL,
        EMP_ZIP INTEGER(5) NOT NULL,
        EMP_PHONE INTEGER(10) NULL,
        EMP_PAGER INTEGER(10) NULL,
        PRIMARY KEY(EMP_ID),
        UNIQUE KEY(EMP_PHONE)
    );

##### 3.4.3 外键约束

**外键是子表里的一个字段，引用父表里的主键。**外键约束是确保表与表之间引用完整性的主要机制。一个被定义为外键的字段用于引用另一个表里的主键。

示例，在创建表时声明外键约束：

    CREATE TABLE employee_pay_tbl 
    (
        EMP_ID CHAR(9) NOT NULL,
        POSITION VARCHAR(20) NOT NULL,
        DATE_HIRE DATE NULL,
        PAY_RATE DECIMAL(4,2) NOT NULL,
        DATE_LAST_RAISE DATE NULL,
        CONSTRAINT FK_EMP_ID FOREIGN KEY(EMP_ID) REFERENCES employee_tbl(EMP_ID) 
    );

或者在创建表时不声明外键约束，而是使用ALTER命令创建外键约束，示例：

    CREATE TABLE employee_pay_tbl 
    (
        EMP_ID CHAR(9) NOT NULL,
        POSITION VARCHAR(20) NOT NULL,
        DATE_HIRE DATE NULL,
        PAY_RATE DECIMAL(4,2) NOT NULL,
        DATE_LAST_RAISE DATE NULL
    );
    ALTER TABLE employee_pay_tbl
    ADD CONSTRAINT FK_EMP_ID FOREIGN KEY(EMP_ID) 
    REFERENCES employee_tbl(EMP_ID);

在这个示例中，EMP_ID字段被定义为表EMPLOYEE_PAY_TBL的外键，它引用了表EMPLOYEE_TBL里的EMP_ID字段。这个外键确保了表EMPLOYEE_PAY_TBL里的每个EMP_ID都在表EMPLOYEE_TBL里有对应的EMP_ID。这被称为父/子关系，父表为EMPLOYEE_TBL，子表为EMPLOYEE_PAY_TBL。

子表里的EMP_ID字段引用父表里的EMP_ID字段。为了在子表里插入一个EMP_ID值，它首先要存在一父表的EMP_ID里。类似地，父表里删除一个EMP_ID的值，子表里的EMP_ID值必须全部被删除。

如果需要级联删除，可以在创建表时指明：

    CREATE TABLE employee_pay_tbl 
    (
        EMP_ID CHAR(9) NOT NULL,
        POSITION VARCHAR(20) NOT NULL,
        DATE_HIRE DATE NULL,
        PAY_RATE DECIMAL(4,2) NOT NULL,
        DATE_LAST_RAISE DATE NULL,
        CONSTRAINT FK_EMP_ID FOREIGN KEY(EMP_ID) REFERENCES employee_tbl(EMP_ID) ON DELETE CASCADE
    );

或使用ALTER命令：

    CREATE TABLE employee_pay_tbl
    (
        EMP_ID CHAR(9) NOT NULL,
        POSITION VARCHAR(20) NOT NULL,
        DATE_HIRE DATE NULL,
        PAY_RATE DECIMAL(4,2) NOT NULL,
        DATE_LAST_RAISE DATE NULL
    );
    ALTER TABLE employee_pay_tbl
    ADD CONSTRAINT FK_EMP_ID FOREIGN KEY(EMP_ID) 
    REFERENCES employee_tbl(EMP_ID) ON DELETE CASCADE;

##### 3.4.4 NOT NULL 约束

NOT NULL也是一个可以用于字段的约束，它不允许字段包含NULL值。在没有指定NOT NULL时，字段默认为NULL，也就是可以是NULL值。

##### 3.4.5 检查约束（MySQL不支持）

检查（CHK）约束用于检查输入到特定字段的数据的有效性，可以提供后端的数据库编辑，虽然编辑通常是在前端程序里完成的。检查约束为数据提供了另一层保护。

以Oracle为例：

    CREATE TABLE EMPLOYEE_CHECK_TST
    (
        EMP_ID CHAR(9) NOT NULL,
        EMP_NAME VARCHAR2(40) NOT NULL,
        EMP_ST_ADDR VARCHAR2(20) NOT NULL,
        EMP_CITY VARCHAR2(15) NOT NULL,
        EMP_ST CHAR(2) NOT NULL,
        EMP_ZIP NUMBER(5) NOT NULL,
        EMP_PHONE NUMBER(10),
        EMP_PAGER NUMBER(10),
        PRIMARY KEY(EMP_ID),
        CONSTRAINT CHK_EMP_ZIP CHECK(EMP_ZIP = '46234')
    );

字段EMP_ZIP增加了检查约束，确保输入到这个表中该字段的值都是“46234”。

##### 3.4.6 去除约束

利用ALTER TABLE命令的DROP CONSTRAINT选项可以去除已经定义的约束（MySQL不支持该写法）。

在MySQL下删除表employee_pay_tbl的外键约束：

    ALTER TABLE employee_pay_tbl DROP FOREIGN KEY FK_EMP_ID;

在MySQL中删除主键约束：

    ALTER TABLE employee_tbl DROP PRIMARY KEY;

### 第4章 规格化过程

将原始数据库分解为表的过程被称为规格化。数据库开发人员利用规格化过程来设计数据库，使其更便于组织和管理，同时确保数据在整个数据库里的正确性。这一过程在各种RDBMS中都是一样的。

#### 4.1 规格化数据库

规格化是去除数据库里荣誉数据的过程，在设计和重新设计数据库时使用。它是一组减少数据冗余来优化数据库的指导方针，具体的方针被称为规格形式。

##### 4.1.1 原始数据库

在没有经过规格化的数据库里，有些数据可能会出现在多个不同的表里，而且没有什么明显的原因。这样对安全、磁盘利用、查询速度、数据库更新都不好，特别是可能产生数据完整性问题。在规格化之前。数据库里的数据并没有从逻辑上被分解到较小的、更易于管理的表里。

示例：

- COMPANY_DATABASE:
    + emp_id
    + last_name
    + first_name
    + middle_name
    + address
    + city
    + state
    + zip
    + phone
    + pager
    + position
    + date_hire
    + pay_rate
    + bonus
    + date_last_raise
    + cust_id
    + cust_name
    + cust_address
    + cust_city
    + cust_state
    + cust_zip
    + cust_phone
    + cust_fax
    + ord_num
    + qty
    + ord_date
    + prod_id
    + prod_desc
    + cost

**
在数据库逻辑设计过程中，确定原始数据库里的信息由什么组成是第一个也是最重要的步骤，我们必须了解组成数据库的全部数据元素，才能有效地使用规格化技术。只有用必要的时间收集所需要的数据集，才能避免因为丢失数据元素而重新设计数据库。
**
##### 4.1.2 数据库逻辑设计

任何数据库设计都要考虑终端用户。数据库逻辑设计也被称为逻辑建模，是把数据安排到逻辑的、有组织的对象组，以便于维护的过程。数据库的逻辑设计应该减少数据重复。

在设计时要考虑的域用户相关的因素包括：

- 数据库里应该保存什么数据
- 用户如何访问数据库
- 用户需要什么权限
- 数据库里的数据如何分组
- 哪些数据最经常被访问
- 全部数据与数据库如何关联
- 采取什么措施保证数据的正确性
- 采取什么措施减少数据冗余
- 采取什么措施让负责维护数据的用户更易于使用数据库

##### 4.1.3 规格形式

规格形式是衡量数据库被规格化级别的一种方式。

常见的三种规格形式：

- 第一规格形式
- 第二规格形式
- 第三规格形式

在这三种主要的规格形式中，每一种都依赖于前一种形式所采用的规格化步骤。

**
*第一规格形式*
**

第一规格和形式的目标是把原始数据分解到表中。在所有表都设计完成之后，给大多数表或全部表设置主键。主键必须是个唯一的值，所以在选取主键时应该尽量选择能够从本质上唯一区别数据的元素。

COMPANY_DATABASE经过第一规格形式后被分解为三个表：

EMPLOYEE_TBL|CUSTOMER_TBL|PRODUCTS_TBL
-----|-----|-----
emp_id | cust_id | prod_id
last_name | cust_name | prod_desc
first_name | cust_address | cost
middle_name | cust_city
address | cust_state
city | cust_zip
state | cust_phone
zip | cust_fax
phone | ord_num
pager | qty
position | ord_date
date_hire | 
pay_rate | 
bonus | 
date_last_raise | 

**
*第二规格形式*
**

第二规格形式的目标是提取对主键仅有部分依赖的数据，把他们保存到令一个表里。

在第一规格形式的基础上进行分解：

- EMPLOYEE_TBL:
    + EMPLOYEE_TBL
    + EMPLOYEE_PAY_TBL
- CUSTOMER_TBL:
    + CUSTOMER_TBL
    + ORDERS_TBL
- PRODUCTS_TBL

EMPLOYEE_TBL|EMPLOYEE_PAY_TBL|CUSTOMER_TBL|ORDERS_TBL|PRODUCTS_TBL
-----|-----|-----|-----|-----
emp_id | emp_id | cust_id | ord_num | prod_id
last_name | position | cust_name | prod_id | prod_desc
first_name | postion_desc | cust_address | qty | cost
middle_name | date_hire | cust_city | ord_date
address | pay_rate | cust_state
city | bonus | cust_zip
state | date_last_raise | cust_zip 
zip |  | cust_phone
phone | | cust_fax
pager

EMPLOYEE_TBL被分解为两个表，分别是EMPLOYEE_TBL和EMPLOYEE_PAY_TBL。雇员的个人信息是依赖主键（EMP_ID）的，保留在EMPLOYEE_TBL中的字段都是如此。另一方面与EMP_ID仅部分依赖的信息被转移到EMPLOYEE_PAY_TBL中。两个表均包含主键EMP_ID，用于在两个表之间匹配对应的数据。

CUSTOMER_TBL被分解为两个表，分别是CUSTOMER_TBL和ORDERS_TBL。顾客的订单信息依赖于每一个CUST_ID，但与顾客的一般信息没有直接依赖关系。

**
*第三规格形式*
**

第三规格形式的目标是删除表里不依赖于主键的数据。

在第二规格形式的基础上进行分解：


+ EMPLOYEE_TBL
+ EMPLOYEE_PAY_TBL
    * EMPLOYEE_PAY_TBL
    * POSITION_TBL
+ CUSTOMER_TBL
+ ORDERS_TBL
+ PRODUCTS_TBL

EMPLOYEE_TBL|EMPLOYEE_PAY_TBL|POSITION_TBL|CUSTOMER_TBL|ORDERS_TBL|PRODUCTS_TBL
-----|-----|-----|-----|-----|-----
emp_id | emp_id | position | cust_id | ord_num | prod_id
last_name | position | postion_desc | cust_name | prod_id | prod_desc
first_name | date_hire |  | cust_address | qty | cost
middle_name | pay_rate |  | cust_city | ord_date
address | bonus |  | cust_state
city | date_last_raise |  | cust_zip
state |  |  | cust_zip 
zip |  |  | cust_phone
phone |  |  | cust_fax
pager

这里创建了一个新表来实现第三规格形式。EMPLOYEE_PAY_TBL被分解为两个表：EMPLOYEE_PAY_TBL和POSITION_TBL。前者用于保存雇员的实际支付信息，后者保存职位描述。

##### 4.1.4 规格化的优缺点

- 优点
    + 更好的数据库整体组织性
    + 减少冗余数据
    + 数据库内部数据一致性
    + 更灵活的数据库设计
    + 更好地处理数据库安全
    + 加强引用整体性的概念
- 缺点
    + 降低数据库性能

#### 4.2 去规格化数据库

去规格化是修改规格化数据库的表的构成，在可控制的数据冗余范围内提高数据库性能。

**尝试提高性能是进行去规格化数据库的唯一原因。**

去规格化也是有代价的，它增加了数据冗余，虽然提高了性能，但需要付出更多的精力来处理相关数据。

### 第5章 操作数据

数据操作语言（DML）用于修改关系型数据库里的数据和表。

#### 5.1 数据操作概述

数据操作语言使数据库用户能够对关系型数据库里的数据进行修改。SQL中的3个基本DML命令是：

- INSERT
- UPDATE
- DELETE

SELECT命令可以与DML命令配合使用。

#### 5.2 用新数据填充表

在用数据填充表时，有很多因素会影响什么数据以及多少数据可以输入到表里。主要因素包括现有的表约束、表的物理尺寸、列的数据集合、列的长度和其它完整性约束。

##### 5.2.1 把数据插入到表

INSERT语句可以把数据插入到表中，其基本语法如下：

    INSERT INTO table_name VALUES(value1, value2, ..., [NULL]);

在使用这种语法时，必须在VALUES列表中包含表里的每个列，每个值之间使用逗号分隔。字符、日期和时间数据类型的值必须以单引号包围，数值型和NULL不用。表里的每一列都应该有值，并且值的顺序与列在表里的次序一致。

示例，创建表products_tbl：

    CREATE TABLE products_tbl
    (
        PROD_ID CHAR(10) NOT NULL,
        PROD_DESC VARCHAR(25) NOT NULL,
        COST DECIMAL(6,2) NOT NULL
    );

插入数据：

    INSERT INTO products_tbl VALUES('11235', 'WITCH COSTUME', 29.99);
    INSERT INTO products_tbl VALUES('222', 'PLASTIC PUMPKIN 18 INCH', 7.75);
    INSERT INTO products_tbl VALUES('13', 'FALSE PARAFFIN TEETH', 1.1);
    INSERT INTO products_tbl VALUES('90', 'LIGHTED LANTERNS', 14.5);
    INSERT INTO products_tbl VALUES('15', 'ASSORTED COSTUMES', 10);
    INSERT INTO products_tbl VALUES('9', 'CANDY CORN', 1.35);
    INSERT INTO products_tbl VALUES('6', 'PUMPKIN CANDY', 1.45);
    INSERT INTO products_tbl VALUES('87', 'PLASTIC SPIDERS', 1.05);
    INSERT INTO products_tbl VALUES('119', 'ASSORTED MASKS', 4.95);
    INSERT INTO products_tbl VALUES('1234', 'KEY CHAIN', 5.95);
    INSERT INTO products_tbl VALUES('2345', 'OAK BOOKSHELF', 59.99);

##### 5.2.2 给表里的指定列插入数据

给表中特定列插入数据的语法如下：

    INSERT INTO table_name (column1, column2, ...)
    VALUES(value1, value2, ...);

示例，创建表orders_tbl：

    CREATE TABLE orders_tbl
    (
        ORD_NUM VARCHAR(10) NOT NULL,
        CUST_ID VARCHAR(10) NOT NULL,
        PROD_ID VARCHAR(10) NOT NULL,
        QTY INT(4) NOT NULL,
        ORD_DATE DATE NULL
    );

对指定列添加数据：

    INSERT INTO orders_tbl (ORD_NUM, CUST_ID, PROD_ID, QTY) VALUES('23A16', '109', '7725', 2);

*INSERT语句里的字段列表顺序并不一定要与表定义中的字段次序相同，但插入值的词语要与字段列表的次序相同。*

##### 5.2.3 从另一个表插入数据

利用INSERT语句和SELECT语句的组合，我们可以根据对另一个表的查询结果把数据插入到表中。

语法如下：

    INSERT INTO table_name1 [(column1, column2, ...)]
    SELECT [*|(column1, column2, ...)]
    FROM table_name2
    [WHERE]

示例，创建表products_tmp：

    CREATE TABLE products_tmp
    (
        PROD_ID VARCHAR(10) NOT NULL,
        PROD_DESC VARCHAR(25) NOT NULL,
        COST DECIMAL(6,2) NOT NULL
    );

基于products_tbl表向products_tmp表添加数据：

    INSERT INTO products_tmp SELECT * FROM products_tbl;

**采用这种语法时，必须确保查询返回的字段与表里的字段或INSERT语句里指定的字段列表具有相同的次序。另外，还要确定SELECT语句返回的数据与要插入数据的表的字段具有兼容的数据类型。**

##### 5.2.4 插入NULL值

当表里字段允许为NULL，并且该值时不确定时，我们可以向其插入NULL值。利用关键字NULL可以在列中插入NULL值。

插入NULL值的语法如下：

    INSERT INTO table_name VALUES(column1, NULL, column3, ...);

示例：

    INSERT INTO orders_tbl (ord_num, cust_id, prod_id, qty, ord_date) VALUES ('23A16', '109', '7725', 2, NULL);

或者可以这样写：

    INSERT INTO orders_tbl VALUES('23A16', '109', '7725', 2);

#### 5.3 更新现有数据

利用UPDATE命令可以修改表里的现有数据。它一般每次只更新数据库里的一个表，但同时可以更新表中的多个字段。根据需要，我们可以只更新表里的一行数据，也可以用一条语句就更新很多行数据。

##### 5.3.1 更新一列数据

UPDATE语句最简单的形式是用于更新表里的一列数据。在更新一列数据时，被更新的记录可以是一条，也可以是多条。

语法如下：

    UPDATE table_name
    SET column_name = value
    [WHERE]

示例：

    UPDATE orders_tbl SET qty = 1 WHERE ord_num = '23A16';

*在使用没有WHERE子句的UPDATE命令时要特别小心。如果没有WHERE子句设置条件，表里的所有记录的相应字段都会被更新。在大多数情况下DML命令都需要使用WHERE子句。*

##### 5.3.2 更新一条或多记录里的多个字段

语法如下：

    UPDATE table_name
    SET column1 = value1
        [column2 = value2, ...]
        [column3 = value3, ...]
    [WHERE]

示例：

    UPDATE orders_tbl SET qty = 1, cust_id = '221' WHERE ord_num = '23A16';

#### 5.4 从表里删除数据

DELETE命令用于从表里删除整行数据，它不能删除某一列的数据。

语法如下：

    DELETE FROM table_name
    [WHERE]

示例：

    DELETE FROM orders_tbl WHERE ord_num = '23A16';

如果要从表里删除指定的数据行，WHERE子句是必须的，我们几乎不会使用没有WHERE子句的DELETE语句。另外还应该先使用SELECT语句对WHERE子句进行测试。

### 第6章 管理数据库事务

#### 6.1 什么是事务

事务是对数据库执行的一个操作单位。它是以逻辑顺序完成的工作单元或工作序列，无论是用户手工操作还是由程序自动操作。

在关系型数据库中，事务由DML完成。事务是对数据库所做的一个或多个修改。

一个事务可以是一个或多个DML语句。在管理事务时，任何指定的事务（DML语句组）都必须作为一个整体来完成，否则其中任何一条语句都不会完成。

事务的特征：

- 所有的事务都有开始和结束；
- 事务可以被保存和撤销；
- 如果事务在中途失败，事务中的任何部分都不会被记录到数据库。

#### 6.2 控制事务

当一个事务被执行并成功完成时，虽然从输出结果来看目标表已经被修改了，但实际上目标表并不是立即被修改的。利用事务控制命令最终认可这个事务可以把事务所做的修改保存到数据库，也可以撤销事务所做的修改。

控制事务的命令：

- COMMIT
- ROLLBACK
- SAVEPOINT

**事务控制命令只与DML命令配合使用。当事务完成之后，事务信息被保存在数据库里的指定区域或临时回退区域，直到事务控制命令出现。当事务控制命令出现时，所做的修改要么被保存到数据库，要么被放弃，然后临时回退区被清空。**

##### 6.2.1 COMMIT命令

COMMIT命令用于把事务所做的修改保存到数据库，他把上一个COMMIT或ROLLBACK命令之后的全部事务都保存到数据库。

语法如下：

    COMMIT [work];

示例：

    START TRANSACTION; 
    -- 开启事务，由于MySQL的autocommit是1，语句会被自动COMMIT，所以需要开启事务以进行事务控制。

    DELETE FROM products_tmp WHERE COST < 14; -- 执行DML
 
    COMMIT; -- 提交事务

##### 6.2.2 ROLLBACK命令

ROLLBACK命令用于撤销还没有被保存到数据库的命令，它只能用于撤销上一个COMMIT或ROLLBACK命令之后的事务。

语法如下：

    ROLLBACK [work];

示例：

    START TRANSACTION;

    DELETE FROM products_tmp WHERE COST < 14;

    ROLLBACK;

回滚后，删除操作被撤销。

ROLLBACK命令会回滚到上一次执行COMMIT或ROLLBACK的状态。

示例：

    START TRANSCATION;
    INSERT ...
    COMMIT; -- 状态1
    UPDATE ...
    ROLLBACK; -- 状态2，UPDATE操作被撤销，回滚至状态1
    DELETE ... 
    ROLLBACK; -- DELETE操作被撤销，回滚至状态2

##### 6.2.3 SAVEPOINT命令

保存点是事务过程中的一个逻辑点，我们可以把事务回退到这个点，而不必回退整个事务。

语法如下：

    SAVEPOINT savepoint_name;
    SAVE TRANSACTION savepoint_name; -- SQL Server

这个命令就是在事务语句之间创建一个保存点。

##### 6.2.4 ROLLBACK TO SAVEPOINT命令

回退到保存点的语法如下：

    ROLLBACK TO savepoint_name -- 保存点的名称必须唯一

示例：

    START TRANSACTION; 

    DELETE FROM products_tmp WHERE COST < 14;

    SAVEPOINT sp1;

    DELETE FROM products_tmp WHERE COST > 50;

    ROLLBACK TO sp1; 

语句执行后，删除COST > 50的操作被撤销。

##### 6.2.5 RELEASE SAVEPOINT命令

这个命令用于删除创建的保存点。在某个保存点被释放后，就不能再利用ROLLBACK命令来撤销这个保存点之后的事务操作了

语法如下：

    RELEASE SAVEPOINT savepoint_name; -- SQL Server不支持该操作

在COMMIT后，所有的保存点都会自动删除，ROLLBACK savepoint操作会删除该保存点后的保存点。示例：

    START TRANSACTION; 
    SAVEPOINT sp1;
    DELETE FROM products_tmp WHERE COST < 14;
    SAVEPOINT sp2;
    DELETE FROM products_tmp WHERE COST > 50;
    ROLLBACK TO sp1; -- 回滚至sp1
    ROLLBACK TO sp2; -- 报错：SAVEPOINT sp2 does not exist
    COMMIT; -- 提交事务
    ROLLBACK TO sp1; -- 报错：SAVEPOINT sp1 does not exist

##### 6.2.6 SET TRANSCATION命令

这个命令用于初始化数据库事务，可以指定事务的特性。我们可以指定事务是制度的或是可以读写的以及设置事务隔离级别。

示例，设置事务是只读的或是可以读写的：

    SET TRANSACTION READ WRITE; 
    SET TRANSACTION READ ONLY; -- 只能读，不能进行DML操作

READ WRITE用于对数据库进行查询和操作数据的事务，READ ONLY用于只进行查询的事务。
如果事务是READ WRITE类型的，数据库必须对数据库对象进行加锁，从而在多个事务同时发生时保持数据完整性。

设置事务隔离级别：

    SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;


#### 6.3 事务控制与数据库性能

低劣的事务控制会降低数据库性能，甚至导致数据库异常终止。反复出现的数据库性能恶化可能是由于在大量插入、更新或删除中缺少事务控制。大规模批处理还会导致临时存储的回退信息不断膨胀，直至出现COMMIT或ROLLBACK命令。

## 第三部分 从查询中获得有效的结果

### 第7章 数据库查询

#### 7.1 什么是查询

查询是使用SELECT语句对数据库进行查询，并根据需要以一种可理解的格式提取数据。

#### 7.2 SELECT语句

SELECT语句代表了SQL里的数据查询语言（DQL），是构成数据库查询的基本语句。

SELECT语句的常用4个关键字（子句）：

- SELECT
- FROM
- WHERE
- ORDER BY

##### 7.2.1 SELECT语句

SELECT语句与FROM子句联合使用构成了最基本的查询语句。

基本语法如下：

    SELECT [*|ALL|DISTINCT column1, column2] 
    FROM table_name1 [, table_name2]

ALL是默认的操作方式，DISTINCT禁止在输出结果里包含重复的行。

##### 7.2.2 FROM子句

FROM子句必须与SELECT语句联合使用，它是任何查询的必要元素。其作用是告诉数据库从那些表里获取所需的数据，它可以指定一个或多个表，但必须至少指定一个表。

语法：

    FROM table_name1 [, table_name2];

##### 7.2.3 WHERE子句

查询里的条件指定了要返回满足什么标准的信息，WHERE子句用于给查询添加条件，从而去除不需要的数据。

WHERE子句里可以有多个条件，他们之间以操作符AND或OR连接。

语法如下：

    ... WHERE [condition1 | expression] [ AND|OR condition2 | expression2];


##### 7.2.4 ORDER BY子句

该子句用于让输出以某种方式进行排序。

语法如下：

    ... ORDER BY column1|integer [ ASC|DESC ];

*ASC（升序）是默认的排序方式。*

SQL中存在一些简化方式。ORDER BY子句里的字段可以缩写为一个整数，这个整数取代了实际的字段名称（排序操作中使用的一个别名），表示字段在SELECT之后列表里的位置。

示例：

    SELECT PROD_DESC, PROD_ID, COST
    FROM product_tbl
    WHERE COST < 20
    ORDER BY 1;

在这个查询中，整数1代表字段PROD_DESC，2代表PROD_ID，3代表COST。则这个查询结果以字段PROD_DESC进行排序。

在一个查询中可以对多个字段进行排序，这时可以使用字段名或相应的整数。这时子句中的字段次序决定了排序过程的完成方式。

示例：

    ORDER BY 1, 2, 3;
    ORDER BY PROD_DESC, COST;

##### 7.2.5 大小写敏感性

一般来说SQL命令和关键字是不区分大小写的，也就是说我们可以以大写或小写来输入命令和关键字，甚至可以混用。

排序规则决定了RDBMS如何解释数据，包括排序方式和大小写敏感性等内容。数据的大小写敏感性很重要，这直接决定了WHERE子句如何匹配记录。不同的数据库实现有不同的规则，例如MySQL和SQL server中默认是大小写不敏感的。这意味着，在进行数据匹配时系统会忽视数据的大小写。而在Oracle中，默认是大小写敏感的，此时数据匹配就会考虑大小写情况。

##### 7.2.6 统计表里的记录数量

统计工作是使用函数COUNT完成的。COUNT函数的语法如下：

    SELECT COUNT([*|column_name]) FROM table_name;

如果要统计表中特定列所出现的值的种类数，需要在COUNT函数中使用DISTINCT关键字。

示例：

    SELECT COUNT(DISTINCT PROD_ID) FROM products_tbl;

##### 7.2.7 从另一个用户表里选择数据

要访问另一个用户表，首先必须有相应的权限。在获得权限后，为了在SELECT语句中访问另一个用户的表，必须在表的名称前添加规划名或相应的用户名。

语法如下

    ... FROM schema_name.table_name ...

*如果要访问的表在数据库中有别名，则不必指定表的规划名。*

##### 7.2.8 使用字段别名

在进行某些查询时，我们使用字段别名来临时命名表的字段，基本语法如下：

    SELECT column_name alias_name FROM table_name;

示例：

    SELECT PROD_DESC,
           PROD_DESC PRODUCT
    FROM products_tbl;

上述查询结果共两列，一列是PROD_DESC，另一列是PRODUCT。两列显示的内容均为字段PROD_DESC的数据。只不过第二个字段使用了别名PRODUCT。

### 第8章 使用操作符对数据进行分类

操作符用于在SELECT命令的WHERE子句中为返回的数据指明更明确的条件。

#### 8.1 什么是SQL操作符

操作符是一个保留字或字符，主要用于SQL语句的WHERE子句来执行操作。操作符用于在SQL语句中指定条件还可以连接一个语句里的多个条件。

操作符主要包括：

- 比较操作符
- 逻辑操作符
- 求反操作符
- 算数操作符

#### 8.2 比较操作符

比较操作符用于在SQL语句里对单个值进行测试，包括：

- =：相等
- <>：不相等
- <：小于
- &gt;：大于
- <=：小于等于
- &gt;=：大于等于

*在进行比较时应该注意数据库的大小写敏感问题。*

#### 8.3 逻辑操作符

逻辑操作符主要包括：

- IS NULL
- BETWEEN
- IN
- LIKE
- EXISTS
- UNIQUE
- ALL
- ANY/SOME

以下使用表数据：

    CREATE TABLE `products_tbl` (
      `PROD_ID` char(10) NOT NULL,
      `PROD_DESC` varchar(25) NOT NULL,
      `COST` decimal(6,2) NOT NULL
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8

PROD_ID | PROD_DESC | COST
-----|-----|-----
11235 |  WITCH COSTUME  | 29.99
222| PLASTIC PUMPKIN 18 INCH |7.75
13 | FALSE PARAFFIN TEETH |   1.10
90 | LIGHTED LANTERNS  |  14.50
15 | ASSORTED COSTUMES |  10.00
9 |  CANDY CORN | 1.35
6 |  PUMPKIN CANDY |  1.45
87 | PLASTIC SPIDERS |1.05
119| ASSORTED MASKS | 4.95
1234 |   KEY CHAIN  | 5.95
2345  |  OAK BOOKSHELF |  59.99

##### 8.3.1 IS NULL

这个操作符用于与NULL值进行比较。

示例：

    SELECT COUNT(*) FROM products_tbl WHERE cost IS NULL;

由于表中COST字段均不为NULL，所以查询结果是0。

##### 8.3.2 BETWEEN

操作符BETWEEN用于寻找一个给定最大值和最小值之间的值，这个最大值和最小值是包含在内的。

示例：

    SELECT * FROM products_tbl WHERE cost BETWEEN 10.00 AND 59.99;

设定最小值为10，最大值为59.99，查询结果为

PROD_ID | PROD_DESC |  COST
-----|-----|-----
11235  | WITCH COSTUME  | 29.99
90  |LIGHTED LANTERNS  |  14.50
15 | ASSORTED COSTUMES |  10.00
2345  |  OAK BOOKSHELF |  59.99

##### 8.3.3 IN

操作符IN用于把一个值与一个指定列表进行比较，当被比较的值至少与列表中的一个值相匹配时，它会返回TRUE。

示例：

    SELECT COUNT(*) FROM products_tbl WHERE cost IN (10.00, 59.99);

查询结果为2，有两条记录的cost值在该列表中。

##### 8.3.4 LIKE

操作符LIKE利用通配符把一个值与类似的值进行比较，通配符有两个：

- 百分号(%)：代表零个、一个或多个字符
- 下划线(_)：代表一个数字或字符

示例：

下面的条件匹配任何已200开头的值：

    WHERE XXX LIKE '200%';

下面的条件匹配任何包含200（在任意位置）的值：

    WHERE XXX LIKE '%200%';

下面的条件匹配第二和第三个字符是0的值：

    WHERE XXX LIKE '_00%';

下面的条件匹配以2开头，而且长度至少为3的值：

    WHERE XXX LIKE '2_%_%';

下面的条件匹配以2结尾的值：

    WHERE XXX LIKE '%2';

下面的条件匹配第二个位置为2，结尾为3的值：

    WHERE XXX LIKE '_2%3';

下面的条件匹配长度为5，以2开头，以3结尾的值：

    WHERE XXX LIKE '2___3';

下面示例搜索产品描述中第二个字符是大写S的记录：

    SELECT * FROM products_tbl WHERE PROD_DESC LIKE '_S%';

得到结果：

PROD_ID | PROD_DESC | COST
-----|-----|-----
15 | ASSORTED COSTUMES | 10.00
119 | ASSORTED MASKS | 4.95

##### 8.3.5 EXISTS

这个操作符用于搜索指定表里是否存在满足特定条件的记录。

语法：

    ... WHERE EXISTS (subquery);

**子查询不返回任何数据，只产生逻辑真值true或逻辑假值false。**

使用EXISTS后，如果子查询至少返回一行数据，则返回TRUE，否则返回FALSE 。

这种执行过程可以通俗的理解为：将外查询表的每一行，代入内查询作为检验，如果内查询返回的结果取非空值，则exists子句返回true，这一行可作为外查询的结果行，否则不能作为结果。

由EXISTS引出的子查询，其目标列表达式通常都用(*)，因为EXISTS的子查询只返回真值或者假值，不返回选择出来的结果，因此，你给什么样的列名最后返回的都是true或者false，所以给出实际列名无意义。

##### 8.3.6 ALL

操作符ALL用于把一个值与另一个集合里的全部值进行比较。

示例：

    SELECT * FROM products_tbl 
    WHERE cost > ALL 
    (SELECT cost FROM products_tbl WHERE PROD_DESC LIKE '%CAN%');

这个查询表示选取的结果应为cost大于所有PROD_DESC中带有“CAN”的条目的COST的条目。

返回结果：

PROD_ID | PROD_DESC | COST
-----|-----|-----
11235 |  WITCH COSTUME |  29.99
222| PLASTIC PUMPKIN 18 INCH |7.75
90 | LIGHTED LANTERNS  |  14.50
15 | ASSORTED COSTUMES |  10.00
119 |ASSORTED MASKS | 4.95
1234 |   KEY CHAIN |  5.95
2345  |  OAK BOOKSHELF  | 59.99

##### 8.3.7 ANY

操作符ANY用以把一个值与另一个列表里的任意值进行比较。

示例：

    SELECT * FROM products_tbl 
    WHERE cost > ANY 
    (SELECT cost FROM products_tbl WHERE PROD_ID LIKE '%2%');

这个查询表示选取的结果应为cost大于任意PROD_ID中带有“2”的条目的COST的条目。

PROD_ID | PROD_DESC | COST
-----|-----|-----
11235 | WITCH COSTUME | 29.99
222 | PLASTIC PUMPKIN 18 INCH | 7.75
90 | LIGHTED LANTERNS |  14.50
15 | ASSORTED COSTUMES | 10.00
2345 | OAK BOOKSHELF | 59.99

*SOME是ANY的别名，可以互换使用。*

#### 8.4 连接操作符

如果想在SQL语句里利用多个条件进行数据查询，可以使用连接操作符对不同的条件进行连接。连接操作符包括：

- AND
- OR

*当SQL语句里包含多个条件和操作符时，利用圆括号把语句按照逻辑关系进行划分可以提高语句的可读性。当然，不恰当的使用圆括号也会影响输出结果。*

##### 8.4.1 AND

操作符AND让我们可以在一条SQL语句的WHERE子句中使用多个条件。只有由AND连接的条件均为TRUE时WHERE子句才返回TRUE。

示例：

    SELECT * FROM products_tbl WHERE cost > 10 AND cost < 30;

返回结果：

PROD_ID | PROD_DESC | COST
----- | ----- |-----
11235 |  WITCH COSTUME |  29.99
90 | LIGHTED LANTERNS |   14.50

##### 8.4.2 OR

操作符OR可以在SQL语句的WHERE子句里连接多个条件，只要其中一个条件为TRUE，WHERE子句就返回TRUE。

示例：

    SELECT * FROM products_tbl WHERE PROD_ID = '11235' OR cost > 30;

返回结果：

PROD_ID | PROD_DESC | COST
----- | ----- |-----
11235 |  WITCH COSTUME |  29.99
2345  |  OAK BOOKSHELF |  59.99

#### 8.5 求反操作符

对于前述的所有逻辑操作符，我们都可以颠倒他们的条件要求。

操作符NOT可以颠倒逻辑操作符的含义，它可以与其它操作符构成以下几种形式：

- <>,! = (NOT EQUAL)
- NOT BEWTEEN
- NOT IN
- NOT LIKE
- IS NOT NULL
- NOT EXISTS
- NOT UNIQUE

#### 8.6 算数操作符

算数操作符用于在SQL语句里执行算数功能。

传统的4个算数功能是：

- +：加法
- -：减法
- *：乘法
- /：除法

*在组合使用算数运算符时一定要考虑优先级问题。语句中如果没有圆括号可能会导致不准确的结果，因为SQL语句本身的语法即使是正确的，其表示的逻辑也可能不正确。*

### 第9章 汇总查询得到的数据

#### 9.1 什么是汇总函数

函数是SQL中的关键字，用于对字段里的数据进行操作。函数是一个命令，通常与字段名称或表达式联合使用，处理输入的数据并产生结果。SQL包含多种类型的函数，本章主要介绍汇总函数。

汇总函数为SQL语句提供合计信息，基本的汇总函数包括：

- COUNT
- SUM
- MAX
- MIN
- AVG

##### 9.1.1 COUNT函数

COUNT函数用于统计不包括NULL值的记录或字段值，在用于查询之中时，他返回一个数值。它也可以与DISTINCT命令一起使用，从而只统计数据集里不同的记录数量。命令ALL是默认的，在语句中不必明确指定。使用COUNT函数的另一种方式是与*配合，COUNT(*)会统计表里的全部记录数量，包含重复的，也不管字段里是否包含NULL值。

语法如下：

    COUNT [(*) | (DISTINCT|ALL)] (column_name)

*COUNT(\*)不能与DISTINCT一起使用。
与其他形式相比，COUNT(\*)返回的结果稍有不同。如果在COUNT函数种使用\*号，将返回所有的统计数，包括重复项和NULL。如果要统计某一字段的记录数，并且包括NULL，则需要使用ISNULL函数。
COUNT函数统计的是行数，不涉及数据类型。行里的数据可以是任意类型的。
*

##### 9.1.2 SUM函数

SUM函数返回一组记录种某一个字段值的综合。它也可以与DISTINCT一起使用，这时只会计算不同记录之和。

语法如下：

    SUM ([DISTINCT] column_name)

SUM函数处理的字段类型必须是数值型的，不能是其它数据类型的。

*如果被处理的类型可以隐式的转换为数值类型，例如CHAR类型，则可以使用SUM函数。如果不能转换则返回0。*

##### 9.1.3 AVG函数

AVG函数可以计算一组记录的平均值。在于DISTINCT一起使用时，它返回不重复记录的平均值。

语法如下：

    AVG ([DISTINCT] column_name)

*AVG函数只能处理数值型字段。在某些实现里，查询结果可能会被取舍到相应数据类型的精度。*

##### 9.1.4 MAX函数

MAX函数返回一组记录中某个字段的最大值，NULL不在计算范围内。可以使用DISTINCT，但是没有意义。

语法如下：

    MAX ([DISTINCT] column_name)   

##### 9.1.4 MIN函数

MIN函数返回一组记录中某个字段的最小值，NULL不在计算范围内。可以使用DISTINCT，但是没有意义。

    MIN ([DISTINCT] column_name)   

### 第10章 数据排序与分组

这一章介绍如何把查询返回的结果划分为组来提高可读性。

#### 10.1 为什么要对数据进行分组

数据分组是按照逻辑次序把具有重复值的字段进行合并。

数据分组是通过在SELECT语句里使用GROUP BY子句来实现的，从而更高效地显示查询结果。

#### 10.2 GROUP BY子句

GROUP BY子句与SELECT语句配合使用，把相同的数据划分为组。在SELECT语句里，GROUP BY子句在WHERE子句之后，在ORDER BY子句之前。

语法如下：

    SELECT column1， column2 ...
    FROM table1, table2 ...
    WHERE conditions
    GROUP BY column1, column2 ...
    ORDER BY column1, column2 ...

GROUP BY子句对CPU的运行效率有很大影响，如果我们不对提供给它的数据进行约束，那么后期很可能需要删除大量的无用数据。所以，需要使用WHERE子句来缩小数据范围，从而确保对有用的数据进行分组。

这里也可以加入ORDER BY子句，但RDBMS通常会使用GROUP BY子句中的序列对返回结果进行排序，本章后续内容将对此进行深入介绍。所以除非用户对返回值的顺序由特殊要求，否则一般不会使用ORDER BY子句。

下述使用表：

**employee_tbl:**

    CREATE TABLE employee_tbl
    (
        EMP_ID CHAR(6) NOT NULL,
        LAST_NAME VARCHAR(15) NOT NULL,
        FIRST_NAME VARCHAR(15) NOT NULL,
        CITY VARCHAR(20) NOT NULL
    );

EMP_ID | LAST_NAME | FIRST_NAME | CITY
-----|-----|-----|-----
AX2911 | WILL  |  JOHN  |  NEW YORK
BR1122 | TOMPSON | TIM | D.C.
AE0021 | RYAN  |  FRANK |  GREENWOOD
MI8837 | ARTHUR | ZOE | GREENWOOD
NU3451 | GLASS |  TINA  |  WHITELAND
YB0928 | GLASS |  JACOB  | INDIANPOLIS
IP0992 | WALLACE| MARIAH | WHITELAND
EF2120 | PLEW  |  LINDA  | INDIANPOLIS

**employee_pay_tbl:**

    CREATE TABLE employee_pay_tbl
    (
        EMP_ID CHAR(6) NOT NULL,
        PAY_RATE DECIMAL(4,2) NOT NULL,
        SALARY INT NOT NULL
    );

EMP_ID | PAY_RATE | SALARY
-----|-----|-----
AX2911 | 10.00 | 10000
BR1122 | 11.40 | 20000
AE0021 | 12.10 | 23000
MI8837 | 13.20 | 25000
NU3451 | 14.70 | 31000
YB0928 | 15.30 | 34000
IP0992 | 15.90 | 40000
EF2120 | 16.00 | 42000

**emp_pay_tmp:**

    CREATE TABLE emp_pay_tmp
    (
        CITY VARCHAR(20) NOT NULL,
        LAST_NAM VARCHAR(15) NOT NULL,
        FIRST_NA VARCHAR(15) NOT NULL,
        PAY_RATE DECIMAL(4,2) NULL,
        SALARY INT NULL
    );

CITY  |  LAST_NAM  |  FIRST_NA |  PAY_RATE  |  SALARY
-----|-----|-----|-----|-----
GREENWOOD |  STEPHENS  |  TINA | NULL  |  30000
INDIANAPOLIS  | PLEW  |  LINDA |  14.75   |NULL
WHITELAND |  GLASS |  BRANDON |NULL |  40000
INDIANAPOLIS  |  WALLACE | MARIAH | 11  |NULL
INDIANAPOLIS  |  SPURGEON  |  TIFFANY| 15  |NULL
INDIANAPOLIS  |  GLASS  | JACOB|  NULL  | 20000

##### 10.2.1 分组函数

典型的分组函数----也就是用于GROUP BY子句对数据进行划分的函数，包括：AVG、MAX、MIN、SUM、COUNT。

##### 10.2.2 对选中的数据进行分组

数据分组是个简单的过程。被选中的字段（查询中SELECT之后的字段列表）才能在GROUP BY子句中引用。在GROUP BY子句里可以使用字段名称也可以使用一个整数来代表字段。在对数据进行分组时，分组字段的次序不一定要与SELECT子句里的字段次序相同。

##### 10.2.3 创建分组和使用汇总函数

SELECT语句在使用GROUP BY子句时必须满足一定条件。特别是只有被选中的字段才能出现在GROUP BY子句中，虽然顺序不必相同。

示例，从表employee_tbl里选择字段EMP_ID和CITY，并且对返回的数据先根据CITY，再根据EMP_ID分组：

    SELECT EMP_ID, CITY
    FROM employee_tbl
    GROUP BY CITY, EMP_ID;

返回结果：

EMP_ID | CITY
-----|-----
BR1122 | D.C.
AE0021 | GREENWOOD
MI8837 | GREENWOOD
EF2120 | INDIANPOLIS
YB0928 | INDIANPOLIS
AX2911 | NEW YORK
IP0992 | WHITELAND
NU3451 | WHITELAND

示例，从表employee_pay_tbl里返回全部薪水的总和：

    SELECT SUM(SALARY) TOTAL_SALARY FROM employee_pay_tbl; 

示例，从表employee_pay_tbl里返回不同薪水的总和：

    SELECT SUM(SALARY) FROM employee_pay_tbl GROUP BY SALARY; 

在第一个示例中，我们可以得到employee_tbl表中包含5个不同的城市：

- D.C.
- GREENWOOD
- INDIANPOLIS
- NEW YORK
- WHITELAND

下面的SQL语句用于统计每个城市记录的数量，这时可以看到每个不同城市的记录总和，因为使用了GROUP BY语句：

    SELECT CITY, COUNT(*) FROM employee_tbl GROUP BY CITY;

返回结果：

CITY  |  COUNT(*)
-----|-----
D.C. |   1
GREENWOOD |  2
INDIANPOLIS |2
NEW YORK  |  1
WHITELAND |  2

下面对临时表emp_pay_tmp进行查询，利用汇总函数AVG获得每个不同城市的平均小时工资和薪水：

    SELECT CITY, AVG(PAY_RATE), AVG(SALARY) 
    FROM emp_pay_tmp 
    GROUP BY CITY;

返回结果：

CITY  |  AVG(PAY_RATE) |  AVG(SALARY)
-----|-----|-----
GREENWOOD   | NULL|  30000.0000
INDIANAPOLIS  |  13.583333   | 20000.0000
WHITELAND   | NULL|  40000.0000

对分组查询进行排序，先是2然后是3，返回INDIAPOLIS和WHITELAND的平均小时工资和薪水：

    SELECT CITY, AVG(PAY_RATE), AVG(SALARY) 
    FROM emp_pay_tmp 
    WHERE CITY IN ('INDIANAPOLIS', 'WHITELAND') 
    GROUP BY CITY 
    ORDER BY 2, 3;

返回结果：

*对于MySQL来说，asc排序时NULL默认被放在最前。*

CITY  |  AVG(PAY_RATE) |  AVG(SALARY)
-----|-----|-----
WHITELAND   |NULL |  40000.0000
INDIANAPOLIS  |  13.583333 |  20000.0000

组合使用MAX、MIN函数与GROUP BY子句：

    SELECT CITY CITY, MAX(PAY_RATE), MIN(SALARY) 
    FROM emp_pay_tmp 
    GROUP BY CITY;

返回结果：

CITY  |  MAX(PAY_RATE) | MIN(SALARY)
-----|-----|-----
GREENWOOD  |NULL | 30000
INDIANAPOLIS  |  15.00  | 20000
WHITELAND   |NULL |  40000

##### 10.2.4 以整数代表字段名称

与ORDER BY子句相同，GROUP BY子句里也可以使用整数代表字段名称。

例如：

    SELECT CITY CITY, MAX(PAY_RATE), MIN(SALARY) 
    FROM emp_pay_tmp 
    GROUP BY 1;

同样返回结果：

CITY  |  MAX(PAY_RATE) | MIN(SALARY)
-----|-----|-----
GREENWOOD  | NULL| 30000
INDIANAPOLIS  |  15.00  | 20000
WHITELAND   |NULL |  40000

#### 10.3 GROUP BY与ORDER BY

二者的相同之处是它们都对数据进行排序。ORDER BY子句专门用于对查询得到的数据进行排序；GROUP BY子句把查询得到的数据排序为适当的分组

用GROUP BY子句实现排序操作的区别与缺点是：

- 所有被选中的、非汇总函数的字段必须列在GROUP BY子句中（MySQL不用）
- 除非需要使用汇总函数，否则使用GROUP BY子句进行排序通常是没有必要的

#### 10.4 CUBE（MySQL不支持）和ROLLUP语句

在某些情况下，对分组数据进行小计是很有用的。ANSI SQL提供了CUBE和ROLLUP语句来解决这个问题。

ROLLUP语句可以用来小计，即在全部分组数据的基础上，对其中的一部分和进行汇总。

假设对(A, B, C)进行ROLLUP，首先会对(A, B, C)进行GROUP BY，然后对(A, B)进行GROUP BY，然后是(A)进行GROUP BY，最后对全表进行GROUP BY操作。

语法如下：

SQL Server / Oracle

    GROUP BY ROLLUP(ordered  column list of grouping sets)

MySQL

    GROUP BY order column list of grouping sets WITH ROLLUP

下述使用表：

    CREATE TABLE ROLLUP
    (
        orderid INT NOT NULL,
        orderdate DATE NOT NULL,
        empid INT NOT NULL,
        custid VARCHAR(10) NOT NULL,
        qty INT NOT NULL,
        PRIMARY KEY(orderid,orderdate)
    );

数据：

orderid orderdate |  empid | custid | qty
-----|-----|-----|-----|-----
1 |  2010-01-02 | 3 |  A |  10
2 |  2010-04-02 | 2 |  B |  20
3 |  2010-05-02 | 1 |  A |  30
4 |  2010-07-02 | 3 |  D |  40
5 |  2011-01-02 | 4 |  A |  20
6 |  2011-01-02 | 3 |  B |  30
7 |  2011-01-02 | 1 |  C |  40
8 |  2009-01-02 | 2 |  A |  10
9 |  2009-01-02 | 3 |  B |  20

首先按照年份分组、统计销量：

    SELECT YEAR(orderdate) YEAR, SUM(qty) SUM 
    FROM ROLLUP 
    GROUP BY YEAR(orderdate);

返回结果：

year  |  sum
-----|-----
2009  |  30
2010  |  100
2011  |  90

然后对单维度年份进行聚合：

    SELECT YEAR(orderdate) YEAR, SUM(qty) SUM 
    FROM ROLLUP  
    GROUP BY YEAR(orderdate) WITH ROLLUP;

返回数据：

year  |  sum
-----|-----
2009  |  30
2010  |  100
2011  |  90
NULL | 220
    
结果与只使用GROUP BY差别不大，只是最后多了一行(NULL, 220)，表示对所有的YEAR再做了一次聚合，即得到订单总数。对单个维度进行ROLLUP操作只是可以在最后得到聚合的数据。

对多个维度进行聚合：

    SELECT empid, custid, YEAR(orderdate) YEAR, SUM(qty) SUM 
    FROM ROLLUP 
    GROUP BY empid, custid, YEAR(orderdate) WITH ROLLUP;

返回结果：

empid |  custid | YEAR  |  SUM
-----|-----|-----|-----
1 |  A |  2010  |  30
1 |  A |  NULL  | 30
1 |  C |  2011 |   40
1 |  C |  NULL  | 40
1 |  NULL  |  NULL  | 70
2 |  A |  2009  |  10
2 |  A |  NULL  | 10
2 |  B |  2010  |  20
2 |  B |  NULL  | 20
2 |  NULL  |  NULL  | 30
3 |  A |  2010  |  10
3 |  A |  NULL  | 10
3 |  B |  2009  |  20
3 |  B |  2011  |  30
3 |  B |  NULL  | 50
3 |  D |  2010 |   40
3 |  D | NULL   | 40
3 |  NULL  |  NULL  | 100
4 |  A |  2011  |  20
4 |  A | NULL |  20
4 | NULL |NULL |  20
 NULL | NULL | NULL |  220

对三次分组都进行了聚合，一个有三种聚合，其中： 

- (null,null,null)表示最后的聚合
- (empid,null,null)表示仅对(empid)一列进行分组的聚合结果
- (empid,custid,null)表示对(empid,custid)两列进行分组的聚合结果
- (empid,custid,year)表示对这3列进行分组的聚合结果——也就是GROUP BY本身聚集

**注意**：

1. ORDER BY不能在ROLLUP中使用，两者为互斥关键字；
2. 如果分组的列包含NULL值，那么ROLLUP的结果可能不正确，因为在ROLLUP中进行的分组统计时，null具有特殊意义。因此在进行ROLLUP时可以先将null转换成一个不可能存在的值，或者没有特别含义的值，比如：IFNULL(xxx,0)
3. mysql中没有像oracle那样的grouping()函数；


ROLLUP是CUBE的一种特殊情况，和ROLLUP一样，cube也是一种对数据的聚合操作。但是ROLLUP只在层次上对数据进行聚合，而CUBE对所有的维度进行聚合。具有N个维度的列，CUBE需要2的N次方次分组操作，而ROLLUP只需要N次分组操作。


1. 假设有n个维度，rollup会有n个聚合：
    - rollup(a,b)统计列包含：(a,b)、(a)、()
    - rollup(a,b,c)统计列包含：(a,b,c)、(a,b)、(a)、()
    - ……以此类推ing……
2. 假设有n个纬度，cube会有2的n次方个聚合：
    - cube(a,b)统计列包含：(a,b)、(a)、(b)、()
    - cube(a,b,c)统计列包含：(a,b,c)、(a,b)、(a,c)、(b,c)、(a)、(b)、(c)、()
    - ……以此类推ing……

#### 10.5 HAVING子句

HAVING子句在SELECT语句里可以与GROUP BY子句联合使用，用于告诉GROUP BY子句在输出里包含哪些分组。HAVING对于GROUP BY的作用相当于WHERE对于SELECT语句的作用。换句话说，WHERE子句用于被设定选择字段的条件，而HAVING子句设置GROUP BY子句形成分组的条件。

基本语法：

    SELECT column1, column2, ...
    FROM table1, table2 ...
    WHERE conditions
    GROUP BY column1, column2, ...
    HAVING conditions
    ORDER BY column1, column2 ...;

示例，选择除了GREENWOOD之外所有城市的平均小时工资和薪水。输出结果按照CITY分组，但只显示平均薪水超过20000的分组，并且按照每个城市的平均薪水进行排序：

    SELECT CITY, AVG(PAY_RATE), AVG(SALARY) 
    FROM emp_pay_tmp 
    WHERE CITY <> 'GREENWOOD' 
    GROUP BY CITY 
    HAVING AVG(SALARY) > 20000 
    ORDER BY 3;

返回结果：

CITY  |  AVG(PAY_RATE) |  AVG(SALARY)
----- | ----- | -----
WHITELAND |  NULL |  40000.0000

*HAVING不一定必须要与GROUP BY联合使用，HAVING本身可以用于条件判断。*

**WHERE和HAVING都可以使用的场景：**

    -- 1.使用where
    select goods_price, goods_name from sw_goods where goods_price > 100
    -- 2.使用having
    select goods_price, goods_name from sw_goods having goods_price > 100

由于在2中已经在select中选择了goods_price字段，所以此时判断也可以用HAVING，效果与WHERE相同。

**可以使用WHERE不能使用HAVING的场景：**

    -- 1.使用where
    select goods_name, goods_number from sw_goods where goods_price > 100
    -- 2.使用having
    select goods_name, goods_number from sw_goods having goods_price > 100

此时使用having会报错，因为在select中没有goods_price字段。

**不可以使用WHERE可以使用HAVING的场景：**

    -- 1.使用where
    select goods_category_id , avg(goods_price) ag from sw_goods where ag > 1000 group by goods_category
    -- 2.使用having
    select goods_category_id , avg(goods_price) ag from sw_goods group by goods_category having ag > 1000

此时使用WHERE会报错，因为表里没有ag字段。这里即使在条件中使用avg(goods_price)也会报错，因为表里同样没有该字段。而HAVING可以根据SELECT查询的内容进行使用。

### 第11章 调整数据的外观

本章介绍如何使用函数来调整输出结果的外观，有些是ANSI标准函数，有些是基于该标准的函数，还有一些是由主要的SQL实现所使用的函数。

*ANSI标准并不是绝对不变的：ANSI规定的标准只是对如何在关系型数据库里使用SQL的一个方针，不同的SQL实现中函数名和实际的语法可能不同，但他们的概念是相同的。*

#### 11.1 ANSI字符函数

字符函数用于在SQL中以不同于存储方式的格式来表示字符串。最常用的ANSI字符函数主要用于进行串接、子串和TRANSLATE等操作。

- 串接：将两个单独的字符串组合为一个
- 子接：从字符串里提取一部分
- TRANSLATE：该函数用于逐字符地把一个字符串变换为另一个

#### 11.2 常用字符函数

字符函数主要用于对字段里的字符串或值进行比较、连接、搜索、提取等。

##### 11.2.1 串接函数

串接函数在不同的实现中略有不同。

假设要将JOHN和SON串接来形成JOHNSON进行查询：

- Oracle：SELECT 'JOHN' || 'SON' ...
- SQL Server：SELECT 'JOHN' + 'SON' ...
- MySQL：SELECT CONCAT('JOHN', 'SON') ...

*SQL Server不支持串接函数。Oracle也有串接函数，但是其串接函数只能用于两个字符串的连接。而MySQL的串接函数可以用于多个字符串的连接。*

如果要使用串接函数连接数字，则需要将数字先转换为字符串。

##### 11.2.2 TRANSLATE函数（MySQL不支持）

TRANSLATE函数搜做字符串里的字符并查找特定的字符，标记找到的位置，然后用替代字符串中对应的字符替换它。

语法如下：

    TRANSLATE (CHARACTER SET, VALUE1, VALUE2)

示例：

    SELECT CITY, TRANSLATE(CITY, 'IND', 'ABC') FROM employee_tbl;

##### 11.2.3 REPLACE

REPLACE函数用于把某个字符或字符串替换为指定的一个字符（或多个字符）。

语法如下：

    REPLACE('value', 'value', [NULL] 'value');

示例，返回雇员表中的全部城市，并且把城市名称里的I全部替换为Z：

    SELECT DISTINCT CITY, REPLACE(CITY, 'I', 'Z') FROM employee_tbl;

返回结果：

CITY  |  replace(CITY, 'I', 'Z')
-----|-----
NEW YORK  |  NEW YORK
D.C.  |  D.C.
GREENWOOD |  GREENWOOD
WHITELAND  | WHZTELAND
INDIANPOLIS |ZNDZANPOLZS

Oracle、SQL Server以及MySQL均支持该函数的ANSI语法结构。

##### 11.2.4 UPPER

UPPER函数可以把字符串里的小写字母转为大写。

语法如下：

    UPPER(character string)

Oracle、SQL Server以及MySQL均支持该函数的ANSI语法结构，同时MySQL还有一个名为UCASE的函数可以完成同样的操作。

##### 11.2.5 LOWER

与UPPER函数相反，LOWER把字符串里的大写字符转为小写字符。

语法：

    LOWER(character string)

示例：

    SELECT DISTINCT CITY, LOWER(CITY) FROM employee_tbl;

返回结果：

CITY  |  LOWER(CITY)
----- | -----
NEW YORK  |  new york
D.C.  |  d.c.
GREENWOOD  | greenwood
WHITELAND |  whiteland
INDIANPOLIS | indianpolis

Oracle、SQL Server以及MySQL均支持该函数的ANSI语法结构，同时MySQL还有一个名为LCASE的函数可以完成同样的操作。

##### 11.2.6 SUBSTR

获取子字符串的函数在不同的实现中名称略有不同。

- Oracle：SUBSTR
- SQL Server：SUBSTRING
- MySQL：SUBSTR/SUBSTRING

语法如下：

    SUBSTR(column_name, starting_position, length)

示例：

    SELECT EMP_ID, SUBSTR(EMP_ID, 2, 4) FROM employee_tbl;

返回结果：

EMP_ID | SUBSTR(EMP_ID, 2, 4)
-----|-----
AX2911 | X291
BR1122 | R112
AE0021 | E002
MI8837 | I883
NU3451 | U345
YB0928 | B092
IP0992 | P099
EF2120  F212

##### 11.2.7 INSTR

INSTR函数用于在字符串里寻找指定的字符串并返回其位置。

语法：

    INSTR(column_name, 'SET', [start_position [, occurrence]])

示例，查询PROD_DESC字段中字母A第一次出现的位置：

    SELECT PROD_DESC, INSTR(PROD_DESC, 'A') FROM products_tbl;

返回结果：

PROD_DESC | INstr(PROD_DESC, 'A')
-----|-----
WITCH COSTUME |  0
PLASTIC PUMPKIN 18 INCH| 3
FALSE PARAFFIN TEETH  |  2
LIGHTED LANTERNS  |  10
ASSORTED COSTUMES |  1
CANDY CORN | 2
PUMPKIN CANDY  | 10
PLASTIC SPIDERS | 3
ASSORTED MASKS | 1
KEY CHAIN |  7
OAK BOOKSHELF   2

如果被查询的字符串不存在，则返回0。

##### 11.2.8 LTRIM

LTRIM函数是另一种截取部分字符串的方式，它与SUBSTRING属于同一家族。LTRIM用于从左剪除字符串里的字符。

语法如下：

    LTRIM(character_string [, 'SET'])

示例：

    SELECT LTRIM(FIRST_NAME, 'LES') 
    FROM customer_tbl 
    WHERE FIRST_NAME = 'LESLIE';

LTRIM会剪除被搜索的字符串在目标字符串里最后一次出现位置之左的全部字符。

**MySQL的LTRIM函数只能有第一个参数。**即MySQL中，该函数只能用于去除左空格，不能去除指定的字符串。

示例：

    SELECT ' Hellow', LTRIM(' Hellow');

##### 11.2.9 RTRIM

RTRIM函数是另一种截取部分字符串的方式，它与SUBSTRING属于同一家族。RTRIM用于从右剪除字符串里的字符。

语法如下：

    RTRIM(character_string [, 'SET'])

示例：

    SELECT RTRIM(FIRST_NAME, 'ON') FROM employee_tbl WHERE FIRST_NAME = 'BRANDON';

**MySQL的RTRIM函数只能有第一个参数。**即MySQL中，该函数只能用于去除右空格，不能去除指定的字符串。 

##### 11.2.10 DECODE

DECODE函数可以在字符串里搜索一个值或字符串，如果找到了，就在结果里显示另一个字符串。

语法如下：

    DECODE(column_namem 'SERACH1', 'RETURN1'[, 'SERACH2', 'RETURN2',..., 'DEFAULT_VALUE']) 

示例：

    SELECT CITY, DECODE(CITY, 'INDIANPOLIS', 'INDY', 'GREENWOOD', 'GREEN', 'OTHER') 
    FROM employee_tbl;

上述SQL语句在MySQL中不能执行，MySQL具有不同的实现。

在MySQL中DECODE函数用于还原ENCODE函数编码后的字符串，示例：

    SELECT DECODE(ENCODE('text','rightKey'), 'rightKey');

返回结果：

    text

如果使用不同的key：

    SELECT DECODE(ENCODE('text','rightKey'), 'wrongKey');

结果会返回乱码：
    
    ���

#### 11.3 其它字符函数

##### 11.3.1 LENGTH

LENGTH函数用于获取字符串、数字、日期或表达式的长度。

语法如下：

    LENGTH(CHARACTER_STRING)

示例：

    SELECT PROD_DESC, LENGTH(PROD_DESC) FROM products_tbl;

返回结果：

PROD_DESC |  LENGTH(PROD_DESC)
-----|-----
WITCH COSTUME  | 13
PLASTIC PUMPKIN 18 INCH| 23
FALSE PARAFFIN TEETH  |  20
LIGHTED LANTERNS  |  16
ASSORTED COSTUMES |  17
CANDY CORN | 10
PUMPKIN CANDY |  13
PLASTIC SPIDERS| 15
ASSORTED MASKS | 14
KEY CHAIN |  9
OAK BOOKSHELF   13

MySQL和Oracle均使用该函数，SQL Server使用函数LEN。

##### 13.3.2 IFNULL

IFNULL函数用于在一个表达式是NULL时从另一个表达式获取值，但值与替代值必须是同一数据类型。

语法如下：

    IFNULL(COLUMN_NAME, 'SUBSTITUTION')

示例：

    SELECT PAY_RATE, IFNULL(PAY_RATE, 0.000) FROM emp_pay_tmp;

返回结果：

PAY_RATE  |  IFnull(PAY_RATE, 0.000)
----- | -----
NULL | 0.000
14.75  | 14.750
NULL | 0.000
11.00 |  11.000
15.00  | 15.000
NULL | 0.000

只有MySQL支持该函数。

##### 13.3.3 COALESCE

该函数也是用指定值替代NULL值，这一点与IFNULL相同。其不同点在于，它可以接受一个数据列表，依次检查其中每一个值，并返回第一个非NULL值。如果没有找到一个非NULL值，他会返回一个NULL值。

示例：

    SELECT FIRST_NA, COALESCE(PAY_RATE, SALARY) FROM emp_pay_tmp;

返回结果：

FIRST_NA  |  COALESCE(PAY_RATE, SALARY)
-----|-----
TINA  |  30000.00
LINDA  | 14.75
BRANDON | 40000.00
MARIAH | 11.00
TIFFANY | 15.00
JACOB   20000.00

##### 13.3.4 LPAD

左填充函数用于在字符串左侧添加字符串或空格。

语法如下：

    LPAD(CHARACTER SET)；

示例：

    -- 在CITY字段左侧添加字符"."，使其总长度达到15个字符。
    SELECT CITY, LPAD(CITY, 15, '.') FROM emp_pay_tmp;

返回结果：

CITY | LPAD(CITY, 15, '.')
-----|-----
GREENWOOD  | ......GREENWOOD
INDIANAPOLIS  |  ...INDIANAPOLIS
WHITELAND |  ......WHITELAND
INDIANAPOLIS |   ...INDIANAPOLIS
INDIANAPOLIS |   ...INDIANAPOLIS
INDIANAPOLIS |   ...INDIANAPOLIS

SQL Server不支持该函数。

##### 13.3.5 RPAD

右填充函数用于在字符串右侧添加字符串或空格。

语法如下：

    RPAD(CHARACTER SET)；

示例：

    -- 在CITY字段右侧添加字符"."，使其总长度达到15个字符。
    SELECT CITY, RPAD(CITY, 15, '.') FROM emp_pay_tmp;

返回结果：

CITY | LPAD(CITY, 15, '.')
-----|-----
GREENWOOD  | GREENWOOD......
INDIANAPOLIS |   INDIANAPOLIS...
WHITELAND  | WHITELAND......
INDIANAPOLIS  |  INDIANAPOLIS...
INDIANAPOLIS  | INDIANAPOLIS...
INDIANAPOLIS  | INDIANAPOLIS...

SQL Server不支持该函数。

##### 13.3.5 ASCII

ASCII函数返回字符串最左侧字符的ASCII码。

语法如下：

    ASCII(CHARACTER SET)

示例：

    SELECT LAST_NAM, ASCII(LAST_NAM) FROM emp_pay_tmp;

返回数据：

LAST_NAM | ASCII(LAST_NAM)
-----|-----
STEPHENS  |  83
PLEW  |  80
GLASS |  71
WALLACE | 87
SPURGEON  |  83
GLASS   71

#### 11.4 算术函数

在多个不同实现之间，算术函数是相对比较标准的。算术函数可以对数据库里的值根据算数规则进行运算。

常见的算术函数：

- 绝对值：ABS
- 舍入：ROUND
- 平方根：SQRT
- 符号：SIGN
- 幂：POWER
- 上限：CEIL
- 下限：FLOOR
- 指数：EXP
- 三角函数：SIN、COS、TAN

大多数算术函数的语法是：

    FUNCTION(EXPRESSION)

#### 11.5 转换函数

转换函数把数据类型从一种转换为另一种。

常见的转换：

- 字符到数字
- 数字到字符
- 字符到日期
- 日期到字符

##### 11.5.1 字符串转换为数字

数值数据类型与字符串数据类型有两个主要区别：

- 算数表达式和函数可以用于数值
- 在输出结果里，数值是右对齐的，而字符串是左对齐的

对于要转换为数值的字符串来说，其中的字符必须是0~9.另外加号、减号和句点可以分别用来表示正数、负数和小数。

Oracle使用TO_NUMBER进行转换，示例：

    SELECT EMP_ID, TO_NUMBER(EMP_ID) FROM employee_pay_tbl;

MySQL中需要使用CAST函数，并需要填入具体类型，例如：

    SELECT CAST('11' AS UNSIGNED INT);

此时结果返回的是作为无符号整形值的11。

##### 11.5.2 数字转换为字符串

Oracle中使用TO_CHAR函数进行转化。

示例：

    SELECT PAY_RATE, TO_CHAR(PAY_RATE) 
    FROM employee_pay_tbl 
    WHERE PAY_RATE IS NOT NULL;

MySQL中使用CAST函数或使用CONCAT函数以连接空字符串的方式进行转换。

示例，使用CAST：

    SELECT CAST(11 AS CHAR); -- 转为VARCHAR报错

示例，使用CONCAT：

    SELECT CONCAT(11, '');

##### 15.5.3 MySQL的CAST函数

**对于MySQL而言，其CAST方法只能向以下类型转换：**

- BINARY[(N)]
- CHAR[(N)]
- DATE
- DATETIME
- DECIMAL[(M[,D])]
- SIGNED
- [INTEGER]
- TIME
- UNSIGNED [INTEGER]

*而这个列表中是没有VARCHAR的。*

### 第12章 日期和时间

#### 12.1 日期是如何存储的

每个实现都有一个默认的日期和时间存储格式，但这种默认格式一般是不同的。

##### 12.1.1 日期和时间的标准数据类型

日期和时间（DATETIME）存储的标准SQL数据类型有三种：

- DATE：直接存储日期。DATE的格式是YYYY-MM-DD，范围从0001-01-01到9999-12-31。
- TIME：直接存储时间。TIME的格式是HH:MI:SS:nn...，范围从00:00:00...到23:59:61.999...。
- TIMESTAMP：直接存储日期和时间。TIMESTAMP的格式是YYYY-MM-DD HH:MI:SS:nn...，范围从0001-01-01 00:00:00...到9999-12-31 23:59:61.999...。

##### 12.1.2 DATETIME元素

DATETIME元素是属于日期和时间的元素，包含在DATETIME定义里。以下列出了DATETIME中必须有的元素及其取值范围：

DATETIME元素 | 有效范围
-----|-----
YEAR|0001~9999
MONTH|01~12
DAY|01~31
HOUR|00~23
MINUTE|00~59
SECOND|00.000...~61.999...

其中61.999用于插入或略去闰秒。如果数据以DATETIME格式存储在数据库中，润秒和闰年这样的日期调整是由数据库在内部完成的。

##### 12.1.3

与其它数据类型一样，每种实现都有自己的形式和语法来处理日期和时间。

- Oracle
    + DATE：存储日期和时间信息
- SQL Server
    + DATETIME：存储日期和时间信息
    + SMALLDATETIME：存储日期和时间信息，但是范围小于DATETIME
    + DATE：存储日期值
    + TIME：存储时间值
- MySQL
    + DATETIME：存储日期和时间信息
    + TIMESTAMP：存储日期和时间信息
    + DATE：存储日期值
    + TIME：存储时间值
    + YEAR：单字节，表示年

#### 12.2 日期函数

日期函数在每个不同实现中有所区别。类似于字符串函数，日期函数用于调整日期和时间数据的外观，以适当的方式显示日期和时间数据、进行比较、计算日期之间的间隔。

##### 12.2.1 当前日期

在很多时候需要从数据库获取当前日期，最常见的是用来与存储日期进行比较，或是作为某种时间标记。

从根本上来说，当前日期保存在数据库所在的计算机上时，被称为系统日期。数据库通过与操作系统进行交互可以获取系统日期。

对于不同的数据库实现，获取系统的时间的方法不同。

- Oracle：SELECT SYSDATE FROM DUAL;
- SQL Server：SELECT GETDATE();
- MySQL：SELECT NOW();

##### 12.2.2 时区

在处理日期和时间信息时，可能要考虑时区。如果在维护数据时需要考虑时区，我们需要处理时区和进行时间的转换（如果SQL实现中有相关的函数）。

常见时区及其缩写：

缩写 | 定义
-----|-----
AST、ADT | 太平标准时间、太平洋白天时间
BST、BDT | 白令标准时间、白令白天时间
CST、CDT | 中部标准时间、中部白天时间
EST、EDT | 东部标准时间、东部白天时间
GMT | 格林威治标准时间
HST、HDT | 阿拉斯加/夏威夷标准时间、阿拉斯加/夏威夷白天时间
MST、MDT | 山区标准时间、山区白天时间
NST | 纽芬兰标准时间、纽芬兰白天时间
PST、PDT | 太平洋标准时间、太平洋白天时间
YST、YDT | 育空标准时间、育空白天时间

##### 12.2.3 时间与日期相加

日、月预计时间的其它组成部分可以加到日期上，从而进行日期比较或是在WHERE子句中提供更精确的条件。

DATETIME值可以增加时间间隔，根据标准定义，时间间隔用于调整DATETIME的值。

示例：

    -- SQL Server使用DATEADD函数对日期的月份加1
    SELECT DATE_HIRE, DATEADD(MONTH, 1, DATEHIRE) FROM employee_pay_tbl;

    -- Oracle使用ADD_MONTHS函数对日期月份加1
    SELECT DATE_HIRE, ADD_MONTH(DATE_HIRE, 1) FROM employee_pay_tbl;

    -- Oracle使用ADD_MONTHS函数对日期天数加1
    SELECT DATE_HIRE, DATE_HIRE + 1 FROM employee_pay_tbl;

    -- MySQL使用DATE_ADD函数和INTERVAL命令对日期天数加1
    SELECT DATE_HIRE, DATE_ADD(DATE_HIRE, INTERVAL 1 DAY), DATE_HIRE + 1 
    FROM employee_pay_tbl;

返回数据：

DATE_HIRE |  DATE_ADD(DATE_HIRE, INTERVAL 1 DAY) | DATE_HIRE + 1
-----|-----|-----
1970-01-30 | 1970-01-31 | 19700131
1972-02-19 | 1972-02-20 | 19720220
1983-08-01 | 1983-08-02 | 19830802
2000-02-02 | 2000-02-03 | 20000203
2018-03-13 | 2018-03-14 | 20180314
1970-01-28 | 1970-01-29 | 19700129
2018-04-07 | 2018-04-08 | 20180408
1969-12-02 | 1969-12-03 | 19691203

##### 12.2.4 其它日期函数

下表列出了Oracle、SQL Server和MySQL支持的一些日期函数：

<table>
    <tr>
        <td>产品</td>
        <td>日期函数</td>
        <td>用途</td>
    </tr>
    <tr>
        <td rowspan="2">Oracle</td>
        <td>NEXT_DAY</td>
        <td>返回指定日期之后的下一天</td>
    </tr>
    <tr>
        <td>MONTHS_BEWTEEN</td>
        <td>返回两个日期之间相差的月数</td>
    </tr>
    <tr>
        <td rowspan="3">SQL Server</td>
        <td>DATEPART</td>
        <td>返回日期的某个元素的整数值</td>
    </tr>
    <tr>
        <td>DATENAME</td>
        <td>返回日期的某个元素值的文本值</td>
    </tr>
    <tr>
        <td>DATEDIFF</td>
        <td>返回两个日期之间由指定日期元素表示的间隔</td>
    </tr>
    <tr>
        <td rowspan="4">MySQL</td>
        <td>DAYNAME(date)</td>
        <td>显示当前日期是星期几</td>
    </tr>
    <tr>
        <td>DAYOFMONTH(date)</td>
        <td>显示当前日期是该月的第几日</td>
    </tr>
    <tr>
        <td>DAYOFWEEK(date)</td>
        <td>显示当前日期是本周的第几日</td>
    </tr>
    <tr>
        <td>DAYOFYEAR(date)</td>
        <td>显示当前日期是本年的第几日</td>
    </tr>
</table>

#### 12.3 日期转换

很多原因会导致进行日期转换，主要用于转换为DATETIME数据类型，或是其它实现中的其它数据类型。

##### 12.3.1 日期描述

日期描述由格式元素组成，用于从数据库以期望的格式提取日期和时间信息。日期描述并不是在所有实现中都存在。

如果不使用日期描述和某种转换函数，日期和时间信息从数据库里是以默认格式提取的，例如：

- 2010-12-21
- 31-DEC-10
- 2010-10-12 23:12:15.12
- ...

但是假如我们想以如下方式显示日期：DECEMBER 31, 2010应该怎么办？这时我们就需要把日期从DATETIME转为字符串。

下表所列常见的日期元素可以帮助我们从数据库里获取适当的日期时间信息：

- Oracle：略
- SQL Server：略
- MySQL：
    + SECOND：秒
    + MINUTE：分
    + HOUR：时
    + DAY：天
    + MONTH：月
    + YEAR：年
    + MINUTE_SECOND：分和秒
    + HOUR_MINUTE：小时和分
    + DAY_HOUR：天和小时
    + YEAR_MONTH：年和月
    + DAY_HOUR：天和小时
    + YEAR_MONTH：年和月
    + HOUR_SECOND：小时和秒
    + DAY_MINUTE：天和分
    + DAY_SECOND：天和秒

##### 12.3.2 日期转换为字符串

日期转换为字符串是为了改变日期在查询中的输出形式，它是通过转换函数完成的。

MySQL中使用CAST函数或CONCAT函数连接空字符串：

    -- CAST方法：原样返回日期的字符串形式，1970-01-30 ----> 1970-01-30
    SELECT CAST(DATE_HIRE AS CHAR) FROM employee_pay_tbl; 
    -- CONCAT方法：返回去掉“-”的字符串形式，1970-01-30 ----> 19700130
    SELECT CONCAT(DATE_HIRE + '') FROM employee_pay_tbl;

也可以使用DATE_FORMAT方法改变输出格式，语法如下：

    DATE_FORMAT(date, format)

常用的format如下表所示：

<table>
    <tr>
        <th>格式</th>
        <th>描述</th>
    </tr>
    <tr>
        <td>%a</td>
        <td>缩写星期名</td>
    </tr>
    <tr>
        <td>%b</td>
        <td>缩写月名</td>
    </tr>
    <tr>
        <td>%c</td>
        <td>月，数值</td>
    </tr>
    <tr>
        <td>%D</td>
        <td>带有英文前缀的月中的天</td>
    </tr>
    <tr>
        <td>%d</td>
        <td>月的天，数值(00-31)</td>
    </tr>
    <tr>
        <td>%e</td>
        <td>月的天，数值(0-31)</td>
    </tr>
    <tr>
        <td>%f</td>
        <td>微秒</td>
    </tr>
    <tr>
        <td>%H</td>
        <td>小时 (00-23)</td>
    </tr>
    <tr>
        <td>%h</td>
        <td>小时 (01-12)</td>
    </tr>
    <tr>
        <td>%I</td>
        <td>小时 (01-12)</td>
    </tr>
    <tr>
        <td>%i</td>
        <td>分钟，数值(00-59)</td>
    </tr>
    <tr>
        <td>%j</td>
        <td>年的天 (001-366)</td>
    </tr>
    <tr>
        <td>%k</td>
        <td>小时 (0-23)</td>
    </tr>
    <tr>
        <td>%l</td>
        <td>小时 (1-12)</td>
    </tr>
    <tr>
        <td>%M</td>
        <td>月名</td>
    </tr>
    <tr>
        <td>%m</td>
        <td>月，数值(00-12)</td>
    </tr>
    <tr>
        <td>%p</td>
        <td>AM 或 PM</td>
    </tr>
    <tr>
        <td>%r</td>
        <td>时间，12-小时（hh:mm:ss AM 或 PM）</td>
    </tr>
    <tr>
        <td>%S</td>
        <td>秒(00-59)</td>
    </tr>
    <tr>
        <td>%s</td>
        <td>秒(00-59)</td>
    </tr>
    <tr>
        <td>%T</td>
        <td>时间, 24-小时 (hh:mm:ss)</td>
    </tr>
    <tr>
        <td>%U</td>
        <td>周 (00-53) 星期日是一周的第一天</td>
    </tr>
    <tr>
        <td>%u</td>
        <td>周 (00-53) 星期一是一周的第一天</td>
    </tr>
    <tr>
        <td>%V</td>
        <td>周 (01-53) 星期日是一周的第一天，与 %X 使用</td>
    </tr>
    <tr>
        <td>%v</td>
        <td>周 (01-53) 星期一是一周的第一天，与 %x 使用</td>
    </tr>
    <tr>
        <td>%W</td>
        <td>星期名</td>
    </tr>
    <tr>
        <td>%w</td>
        <td>周的天 （0=星期日, 6=星期六）</td>
    </tr>
    <tr>
        <td>%X</td>
        <td>年，其中的星期日是周的第一天，4 位，与 %V 使用</td>
    </tr>
    <tr>
        <td>%x</td>
        <td>年，其中的星期一是周的第一天，4 位，与 %v 使用</td>
    </tr>
    <tr>
        <td>%Y</td>
        <td>年，4 位</td>
    </tr>
    <tr>
        <td>%y</td>
        <td>年，2 位</td>
    </tr>
</table>

示例：

    SELECT DATE_HIRE, DATE_FORMAT(DATE_HIRE, '%Y/%m/%d-%a') FROM employee_pay_tbl;

返回数据：

DATE_HIRE |  date_format(DATE_HIRE, '%Y/%m/%d-%a')
----- | -----
1970-01-30 | 1970/01/30-Fri
1972-02-19 | 1972/02/19-Sat
1983-08-01 | 1983/08/01-Mon
2000-02-02 | 2000/02/02-Wed
2018-03-13 | 2018/03/13-Tue
1970-01-28 | 1970/01/28-Wed
2018-04-07 | 2018/04/07-Sat
1969-12-02 | 1969/12/02-Tue

SQL Server示例如下：

    SELECT DATE_HIRE = DATENAME(MONTH, DATE_HIRE) FROM employee_pay_tbl;

结果返回每条记录的月份，例如：June。

Oracle示例如下：

    SELECT DATE_HIRE, TO_CHAR(DATE_HIRE， 'Month dd, yyyy') HIRE FROM employee_pay_tbl;

结果返回的HIRE字段格式为：May 23， 1999

##### 12.3.3 字符串转换为日期

MySQ中使用STR_TO_DATE函数：

    -- 分隔符、年月日要一致
    SELECT STR_TO_DATE('2017-10-16 15:30:28', '%Y-%m-%d %H:%i:%s') FORMAT_DATE;

SQL Server中使用CONVERT函数：

    SELECT CONVERT('2017-10-16 15:30:28', '%Y-%m-%d %H:%i:%s') AS FORMAT_DATE;

## 第四部分 建立复杂的数据库查询

### 第13章 在查询里结合表

本章介绍如何在一个查询中结合多个表进行查询。

#### 13.1 从多个表中获取数据

能够从多个表选择数据是SQL最强大的特性之一。最实用的查询是要从多个表中获取数据。

关系型数据库为达到简单和易于管理的目的，被分解为较小的、更易于管理的表。正是由于被分解为较小的表，它们通过共有字段（主键、外键）形成相互关联的表，并且能够通过这些字段结合在一起。

既然最终还是要利用重新结合表来获取需要的数据，那么为什么还要对表进行规格化呢？在实际应用中，我们很少从表里选择全部数据，因此最好是根据每个查询的需求进行挑选。虽然数据库规格化会对性能产生一定影响，但从整体来说，变成和维护都更加容易了。需要记住的是，规格化的目的是减少冗余和提高数据完整性。

#### 13.2 结合的类型

结合是把两个或多个表组合在一起来获取数据，不同的实现具有多种结合表的方式。最常用的结合方式为：

- 等值结合（内部结合）
- 非等值结合
- 外部结合
- 自结合

##### 13.2.1 结合条件的位置

在结合表时WHERE子句是必要的，要结合的表列在FROM子句中，而结合是在WHERE子句中完成的。多个操作符可以用于结合表，比如=、<、>、<>、<=、>=、!=、BEWTEEN、LIKE和NOT等，其中最常用的是等于号。

##### 13.2.2 等值结合

最常用也是最重要的结合就是等值结合，也被称为内部结合。等值结合利用通用字段结合两个表，而这个字段通常是每个表里的主键。

等值结合的语法如下：

    SELECT table1.column1, table2.column2 ...
    FROM table1, table2 ...
    WHERE table1.column_name = table2.column_name 
    [AND table1.column_name = table3.column_name]

示例：

    SELECT `employee_tbl`.EMP_ID, `employee_pay_tbl`.DATE_HIRE 
    FROM `employee_tbl`, `employee_pay_tbl` 
    WHERE`employee_tbl`.EMP_ID = `employee_pay_tbl`.EMP_ID;

返回结果：

EMP_ID | DATE_HIRE
-----|-----
AX2911 | 1970-01-30
BR1122 | 1972-02-19
AE0021 | 1983-08-01
MI8837 | 2000-02-02
NU3451 | 2018-03-13
YB0928 | 1970-01-28
IP0992 | 2018-04-07
EF2120 | 1969-12-02

这个SQL语句返回雇员ID和雇佣日期。雇员ID来自于表employee_tbl（虽然其存在于两个表中，但是我们必须指定一个表），而雇佣日期来自于表employee_pay_tbl。由于雇员ID存在于两个表中，所以字段名称前必须加以修饰。

示例：

    SELECT `employee_tbl`.EMP_ID, 
           `employee_tbl`.LAST_NAME, 
           `employee_pay_tbl`.POSITION, 
           `employee_pay_tbl`.DATE_HIRE 
    FROM `employee_tbl`, `employee_pay_tbl` 
    WHERE`employee_tbl`.EMP_ID = `employee_pay_tbl`.EMP_ID;

返回数据：

EMP_ID | LAST_NAME |  POSITION  |  DATE_HIRE
----- | ----- |----- |-----
AX2911 | WILL  |  MARKETING |  1970-01-30
BR1122 | TOMPSON | TEAM LEADER | 1972-02-19
AE0021 | RYAN   | SALES MANAGER |  1983-08-01
MI8837 | ARTHUR | SALESMAN  |  2000-02-02
NU3451 | GLASS  | MARKETING |  2018-03-13
YB0928 | GLASS  | SHIPPER | 1970-01-28
IP0992 | WALLACE | SHIPPER | 2018-04-07
EF2120 | PLEW  |  MARKETING |  1969-12-02

这里EMP_ID和LAST_NAME来自表employee_tbl，POSITION和DATE_HIRE来自表employee_pay_tbl。使它们连接的条件是两个表的EMP_ID字段相等。

SELECT语句中每个字段都以表名作为前缀，从而准确表时各个字段。在查询中，这被称为限定字段，它只有在字段存在于多个表时才有必要。

等值连接的语法也可以通过INNER JOIN来实现，该实现具有更高的可读性。语法如下：

    SELECT table1.column1, table2.column2 ...
    FROM table1
    INNER JOIN table2 ON
    table1.column_name = table2.column_name

示例：

    SELECT `employee_tbl`.EMP_ID,
           `employee_tbl`.LAST_NAME,
           `employee_pay_tbl`.POSITION,
           `employee_pay_tbl`.DATE_HIRE
        FROM `employee_tbl` INNER JOIN `employee_pay_tbl` ON `employee_tbl`.EMP_ID = `employee_pay_tbl`.EMP_ID;

返回结果同上。

##### 13.2.3 使用表的别名

使用表的别名意味着在SQL语句里对表进行重命名，这是一种临时性的改变，表在数据库里的实际名称不会受到影响。给表起别名一般是为了减少键盘输入，从而得到更短更易读的SQL语句。

示例： 

    -- 使用WHERE
    SELECT E.EMP_ID, 
           E.LAST_NAME, 
           EP.POSITION, 
           EP.DATE_HIRE 
    FROM `employee_tbl` E, `employee_pay_tbl` EP 
    WHERE E.EMP_ID = EP.EMP_ID;

    -- 使用INNER JOIN ON
    SELECT E.EMP_ID,
           E.LAST_NAME,
           EP.POSITION,
           EP.DATE_HIRE 
    FROM `employee_tbl` E INNER JOIN `employee_pay_tbl` EP 
    ON E.EMP_ID = EP.EMP_ID;

这里给表employee_tbl命名为E，给表employee_pay_tbl命名为EP。

##### 13.2.4 不等值结合

不等值结合根据同一字段在两个表里的值不相等来实现。

语法如下：

    SELECT table1.column1, table2.column2 ...
    FROM table1, table2 ...
    WHERE table1.column_name != table2.column_name 
    [AND table1.column_name != table3.column_name]

示例：

    SELECT E.EMP_ID, 
           E.LAST_NAME, 
           EP.POSITION 
    FROM `employee_tbl` E, `employee_pay_tbl` EP 
    WHERE E.EMP_ID <> EP.EMP_ID 
    AND E.LAST_NAME IN ('GLASS', 'RYAN') 
    AND E.CITY = 'GREENWOOD';
    GROUP BY EP.POSITION

返回数据：

EMP_ID | LAST_NAME |  POSITION
----- | ----- |-----
AE0021 | RYAN |   MARKETING
AE0021 | RYAN |   SALESMAN
AE0021 | RYAN |   MARKETING
AE0021 | RYAN |   SHIPPER

##### 13.2.5 外部结合

外部结合会返回一个表里的全部记录，即使对应的记录在第二个表里不存在。在很多实现里，外部结合被划分为左外部结合、右外部结合和全外部结合。

外部结合语法一般为：

    FROM TABLE1
    {RIGHT | LEFT | FULL} [OUTER] JOIN
    ON TABLE2

外部结合的概念将在下面两个示例中加以解释。

使用表：products_tbl

PROD_ID | PROD_DESC |  COST
----- | ----- | -----
11235 |  WITCH COSTUME |  29.99
222 | PLASTIC PUMPKIN 18 INCH | 7.75
13 | FALSE PARAFFIN TEETH  |  1.10
90 | LIGHTED LANTERNS  |  14.50
15 | ASSORTED COSTUMES |  10.00
9  | CANDY CORN | 1.35
6  | PUMPKIN CANDY  | 1.45
87 | PLASTIC SPIDERS| 1.05
119| ASSORTED MASKS | 4.95
1234  |  KEY CHAIN  | 5.95
2345  |  OAK BOOKSHELF |  59.99

使用表：orders_tbl

PROD_ID | QTY
----- | -----
11235 |  11
222 | 2
13 | 33
90 | 4
87 | 123
2345  |  29

第一个示例：选择产品描述和订购数量，这两个值取自两个单独的表里。注意并不是每件产品在表orders_tbl中都有相应的记录。这里执行一个普通的等值连接。

    SELECT P.PROD_DESC, O.QTY 
    FROM products_tbl P, orders_tbl O 
    WHERE P.PROD_ID = O.PROD_ID;

返回结果：

PROD_DESC | QTY
----- | -----
WITCH COSTUME |  11
PLASTIC PUMPKIN 18 INCH | 2
FALSE PARAFFIN TEETH  |  33
LIGHTED LANTERNS  |  4
PLASTIC SPIDERS | 123
OAK BOOKSHELF | 29

内连接会忽略两个表对应不上的数据，所以在orders_tbl中不存在的数据并没有查询出来。

第二个示例：使用左外连接

    SELECT P.PROD_DESC, O.QTY 
    FROM products_tbl P 
    LEFT JOIN orders_tbl O 
    ON P.PROD_ID = O.PROD_ID;

返回数据：

PROD_DESC | QTY
----- | -----
WITCH COSTUME |  11
PLASTIC PUMPKIN 18 INCH | 2
FALSE PARAFFIN TEETH  |  33
LIGHTED LANTERNS  |  4
PLASTIC SPIDERS | 123
OAK BOOKSHELF |  29
ASSORTED COSTUMES |  NULL
CANDY CORN | NULL
PUMPKIN CANDY |  NULL
ASSORTED MASKS | NULL
KEY CHAIN |  NULL

这个查询返回了全部的产品，不论它是否有相应的订单。外部结合会包含表products_tbl里的全部记录，不管在表orders_tbl中是否有对应的记录。

- 左外连接：返回要查询的左表字段中的所有行并返回要查询的右表字段结果，如果左表的某行在右表中没有匹配行，则结果集中右表该行数据为NULL。
- 右外连接：返回要查询的右表字段中的所有行并返回要查询的左表字段结果，如果右表的某行在左表中没有匹配行，则结果集中左表该行数据为NULL。

##### 13.2.6 自结合

自结合利用表别名在SQL语句对表进行重命名，像两个表一样把表结合到自身。

语法如下：

    SELECT A.column_name, B.column_name, [ C.column_name]
    FROM table1 A, table2 B [, table3 C]
    WHERE A.column_name = B.column_name
    [ AND A.column_name = C.column_name]

当所需要的数据都位于一个表里，而我们又必须对记录进行一些比较时，就可以使用自结合。

示例：查询employee_tbl表中LAST_NAME相同的雇员

    SELECT A.EMP_ID, A.LAST_NAME, A.FIRST_NAME 
    FROM employee_tbl A, employee_tbl B 
    WHERE A.LAST_NAME = B.LAST_NAME 
    AND A.EMP_ID <> B.EMP_ID;

返回结果：

EMP_ID | LAST_NAME |  FIRST_NAME
----- | -----| -----
YB0928 | GLASS |  JACOB
NU3451 | GLASS |  TINA

自结合同样可以使用INNER JOIN：

    SELECT A.EMP_ID, A.LAST_NAME, A.FIRST_NAME 
    FROM employee_tbl A 
    INNER JOIN employee_tbl B 
    ON A.LAST_NAME = B.LAST_NAME 
    AND A.EMP_ID <> B.EMP_ID;

返回结果相同。

另一个示例，现有下表：ID为员工ID，NAME为员工名字，MGR_ID为该员工对应的主管的ID（JOHN是BOSS）。

ID |  NAME  |  MGR_ID
----- | -----| -----
1 |  JOHN  |  0
2 |  MARY  |  1
3 |  STEVE |  1
4 |  JACK  |  2
5 |  SUE   |  2

现在要列出所有雇员及其主管的名字：

    SELECT A.NAME EMP, B.NAME MANAGER 
    FROM EMP A INNER JOIN EMP B 
    ON A.MGR_ID = B.ID;

返回结果：

EMP | MANAGER
----- | -----
MARY  |  JOHN
STEVE |  JOHN
JACK  |  MARY
SUE| MARY

#### 13.3 需要考虑的事项

在使用结合之前需要考虑一些事情：

- 基于什么字段进行结合
- 是否有公共字段进行结合
- 性能问题

查询中的结合越多，数据库需要完成的工作就越多，也就意味着需要越多的时间来获取数据。在从规格化的数据库里获取数据时，结合是不可避免的，但不恰当的结合会导致严重的性能下降以及不准确的查询结果。

##### 13.3.1 使用基表

如果需要从两个表里获得数据，但是它们又没有公共字段，我们就必须结合另一个表，这个表与前两个表都有公共字段（字段名不一定相同），这个表就被称为基表。

使用表：student

sid | sname
----- | -----
1  | JEFF
2  | SUE
3  | PAUL

使用表：course

cid | cname
----- | -----
1 |  JAVASE
2 |  JAVAEE
3 |  ANDROID

基表：sc_middle

id | student_id | course_id
----- | ----- |-----
1 |  1 |  1
2 |  1 |  2
3 |  2 |  1
4 |  2 |  3
5 |  3 |  1
6 |  3 |  2
7 |  3 |  3

示例：查询每个学生选择的课程

    SELECT C.cname , S.sname 
    FROM course C, student S, sc_middle M 
    WHERE M.student_id = S.sid AND M.course_id = C.cid;

返回结果：

cname |  sname
-----|-----
JAVASE | JEFF
JAVAEE | JEFF
JAVASE | SUE
ANDROID| SUE
JAVASE | PAUL
JAVAEE | PAUL
ANDROID| PAUL

##### 13.3.2 笛卡儿积

笛卡尔积是笛卡尔结合的结果，笛卡尔结合会返回被结合表的全部记录。笛卡儿积也被称为交叉结合。

语法：

    FROM TABLE1, TABLE2 ...
    WHERE conditions

*强烈建议使用WHERE子句以缩小结果集。*

假设有表temp1：

<table>
    <tr>
        <td>X</td>
    </tr>
    <tr>
        <td>A</td>
    </tr>
    <tr>
        <td>B</td>
    </tr>
    <tr>
        <td>C</td>
    </tr>
    <tr>
        <td>D</td>
    </tr>
</table>

有表temp2：

<table>
    <tr>
        <td>Y</td>
    </tr>
    <tr>
        <td>A</td>
    </tr>
    <tr>
        <td>B</td>
    </tr>
    <tr>
        <td>C</td>
    </tr>
    <tr>
        <td>D</td>
    </tr>
</table>

则笛卡儿积：
    
    SELECT X, Y FROM temp1, temp2;

返回结果：

X | Y
-----|-----
A | A
B | A
C | A
D | A
A | B
B | B
C | B
D | B
A | C
B | C
C | C
D | C
A | D
B | D
C | D
D | D

### 第14章 使用子查询定义未确定的数据

本章介绍子查询的相关内容。子查询可以帮助用户更便捷地完成复杂的查询操作。

#### 14.1 什么是子查询

子查询也被称为嵌套查询，是位于另一个查询的WHERE子句里的查询，**它返回的数据通常在主查询里作为一个条件，从而进一步限制数据库返回的数据。**它可以用于SELECT、INSERT、UPDATE以及DELETE语句。

在某些情况下，子查询能够间接地基于一个或多个条件把多个表里的数据关联起来，从而代替结合操作。**当查询里使用子查询时，子查询首先被执行，**然后主查询再根据子查询返回的结果执行。子查询的结果用于在主查询的WHERE子句里处理表达式。子查询可以用于WHERE子句或HAVING子句。逻辑和关系操作符可以用于子查询中，也可以在WHERE和HAVING子句中对子查询进行操作。结合操作、函数、转换和其它选项同样可以在子查询中使用。

子查询必须遵顼以下规则：

- 子查询必须位于圆括号中
- 除非主查询里右多个字段让子查询进行比较，否则子查询的SELECT子句里只能有一个字段
- 子查询中不能使用ORDER BY子句，但可以使用GROUP BY子句
- 返回多条记录的子查询只能与多值操作符配合使用
- SELECT列表里不能引用任何BLOB、ARRAY、CLOB或NCLOB类型的值
- 子查询不能直接被包围在函数里
- 操作符BEWTEEN布恩那个用于子查询，但子查询内部可以使用它。

子查询的基本语法如下：

    SELECT COLUMN_NAME 
    FROM TABLE
    WHERE COLUMN_NAME = 
    (SELECT COLUMN_NAME FROM TABLE WHERE CONDITIONS);

##### 14.1.1 子查询与SELECT语句

虽然子查询也可以用于数据操作语句，但它最主要还是用于SELECT语句里。获取数据给主查询使用。

基本语法如下：

    SELECT COLUMN_NAME [, COLUMN_NAME]
    FROM TABLE1 [, TABLE2]
    WHERE COLUMN_NAME OPERATOR
    (SELECT COLUMN_NAME [, COLUMN_NAME]
    FROM TABLE1 [, TABLE2]
    [WHERE]);

示例：

    SELECT E.EMP_ID, 
           E.LAST_NAME, 
           E.FIRST_NAME, 
           EP.PAY_RATE, 
           EP.SALARY 
    FROM `employee_tbl` E, `employee_pay_tbl` EP 
    WHERE E.EMP_ID = EP.EMP_ID 
    AND 
    EP.PAY_RATE > 
        (SELECT PAY_RATE 
        FROM `employee_pay_tbl` 
        WHERE EMP_ID = 'MI8837');

上述这条SQL语句返回小时工资大于雇员“MI8837”的所有雇员的ID、姓名、小时工资和薪水。

返回结果：

EMP_ID | LAST_NAME |  FIRST_NAME | PAY_RATE |   SALARY
----- | ----- | ----- | ----- | -----
NU3451 | GLASS |  TINA  |  14.70 |  31000
YB0928 | GLASS |  JACOB |  15.30 |  34000
IP0992 | WALLACE | MARIAH | 15.90|   40000
EF2120 | PLEW  |  LINDA |  16.00 |  42000

##### 14.1.2 子查询与INSERT语句

子查询可以与数据操作语言（DML）配合使用。首先是INSERT语句，它将子查询返回的结果插入另一张表。

基本语法如下：

    INSERT INTO TABLE_NAME [ (COLUMN1 [, COLUMN])]
    SELECT [ *|COLUMN1 [,COLUMN2]]
    FROM TABLE1 [, TABLE2]
    [WHERE VALUE OPERATOR]

示例：

创建表rich_employee：

    CREATE TABLE rich_employee
    (
        ID CHAR(6) NOT NULL,
        NAME VARCHAR(20) NOT NULL,
        PAY_RATE DECIMAL(4,2) NOT NULL,
        SALARY INT NOT NULL
    );

SQL语句：查询employee_tbl和employee_pay_tbl中的数据然后插入rich_employee中

    INSERT INTO rich_employee 
    SELECT E.EMP_ID, 
           CONCAT(E.FIRST_NAME, ' ', E.LAST_NAME), 
           EP.PAY_RATE, 
           EP.SALARY 
    FROM `employee_tbl` E, `employee_pay_tbl` EP 
    WHERE E.EMP_ID = EP.EMP_ID 
    AND 
    EP.PAY_RATE > 
        (SELECT PAY_RATE 
        FROM `employee_pay_tbl` 
        WHERE EMP_ID = 'MI8837');

插入后rich_employee表数据为：

ID | NAME |   PAY_RATE |   SALARY
----- | ----- | ----- | ----
NU3451 | TINA GLASS | 14.70 |  31000
YB0928 | JACOB GLASS | 15.30  | 34000
IP0992 | MARIAH WALLACE | 15.90 |  40000
EF2120 | LINDA PLEW | 16.00 |  42000

##### 14.1.3 子查询与UPDATE语句

子查询可以与UPDATE语句配合使用来更新一个表里的一个或多个字段，基本语法如下：

    UPDATE TABLE
    SET COLUMN_NAME [, COLUMN_NAME] =
    (SELECT COLUMN_NAME [, COLUMN_NAME]
    FROM TABLE
    [WHERE])

示例：

    UPDATE employee_pay_tbl 
    SET PAY_RATE = PAY_RATE * 1.1 
    WHERE EMP_ID IN 
    (SELECT EMP_ID FROM employee_tbl WHERE CITY = 'INDIANAPOLIS');

##### 14.1.4 子查询与DELETE语句

子查询也可以与DELETE语句配合使用，其基本语法如下：

    DELETE FROM TABLE_NAME
    [WHERE OPERATOR [VALUE]
    (SELECT COLUMN_NAME 
    FROM TABLE_NAME
    [WHERE])]

示例：

    DELETE FROM employee_pay_tbl 
    WHERE EMP_ID = 
    (SELECT EMP_ID FROM employee_tbl WHERE LAST_NAME = 'GLASS' AND FIRST_NAME = 'JACOB');

#### 14.2 嵌套的子查询

子查询可以嵌入到另一个子查询里，就像子查询嵌套在普通查询中一样。在嵌套的子查询中，最内层的子查询先被执行，然后依次执行外层的子查询直到主查询。

*一个语句中能够嵌套子查询的数量取决于具体的实现。*

基本语法如下：

    SELECT COLUMN_NAME [, COLUMN_NAME]
    FROM TABLE1 [, TABLE2]
    WHERE COLUMN_NAME OPERATOR 
        (SELECT COLUMN_NAME
        FROM TABLE
        WHERE COLUMN_NAME OPERATOR
            (SELECT COLUMN_NAME
            FROM TABLE
            [WHERE COLUMN_NAME OPERATOR VALUE]))

示例：

    SELECT EMP_ID, FIRST_NAME, LAST_NAME 
    FROM employee_tbl 
    WHERE EMP_ID IN 
        (SELECT EMP_ID 
        FROM employee_pay_tbl 
        WHERE PAY_RATE > 
            (SELECT PAY_RATE FROM 
            `employee_pay_tbl` 
            WHERE EMP_ID = 'MI8837'));

返回结果：

EMP_ID | FIRST_NAME | LAST_NAME
-----|-----|-----
NU3451 | TINA  |  GLASS
IP0992 | MARIAH |  WALLACE
EF2120 | LINDA |  PLEW
YB0928 | JACOB |  GLASS

#### 14.3 关联子查询

关联子查询在很多SQL实现里都存在，它的概念数于ANSI标准。关联子查询是依赖主查询里的信息的子查询。这意味着子查询里的表可以与主查询里的表相关联。

    SELECT P.PROD_ID, P.PROD_DESC 
    FROM products_tbl P 
    WHERE 
    (SELECT O.QTY FROM orders_tbl O WHERE P.PROD_ID = O.PROD_ID) > 10;

返回结果：

PROD_ID | PROD_DESC
----- | -----
11235  | WITCH COSTUME
13 | FALSE PARAFFIN TEETH
87 | PLASTIC SPIDERS
2345  |  OAK BOOKSHELF

### 第15章 组合多个查询

本章介绍如何使用操作符UNION、UNION ALL、INTERSECT和EXPECT把多个SQL查询组合为一个。

#### 15.1 单查询与组合查询

单查询是一个SELECT语句，而组合查询具有两个或多个SELECT语句。
组合查询由负责结合两个查询的操作符组成。组合操作用于组合和限制两个SELECT语句的结果，它们可以返回或清除重复的记录。组合操作符可以获得不同字段里的类似数据。

组合查询可以把多个查询的结果组合成一个结果集，而且通常比使用复杂条件的单查询更容易编写。另外组合查询对于数据检索也具有更强的灵活性。

#### 15.2 组合查询操作符

不同的厂商提供的组合操作符略有不同。ANSI标准包括UNION、UNION ALL、INTERSECT和EXCEPT。

以下使用表t1：

id | name  |  age
-----|-----|-----
1 |  小王 | 10
2 |  小宋 | 20
3 |  小白 | 30

表t2：

id | name |   age
-----|-----|-----
1 |  小王 | 10
2 |  小宋 | 22
3 |  小肖 | 31

##### 15.2.1 UNION - - 并集

UNION操作符可以组合两个或多个SELECT语句的结果，不包含重复的记录。换句话说，如果某行的输出存在于一个查询结果里，那么其他查询结果同一行的记录就不会再输出了。**在使用UNION操作符时，每个SELECT语句里必须选择同样数量的字段、同样数量的字段表达式、同样数量的数据类型、同样的次序----但长度不必一样。**

语法如下：

    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]
    UNION
    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]

示例：

    SELECT id, NAME, age FROM t1 
    UNION 
    SELECT id, NAME, age FROM t2;

返回数据：

id | name  |  age
-----|-----|-----
1  | 小王 | 10
2  | 小宋 | 20
3  | 小白 | 30
2  | 小宋 | 22
3  | 小肖 | 31

示例：

    SELECT id, NAME FROM t1 
    UNION 
    SELECT NAME, age FROM t2;

返回结果：

id | name
-----|-----
1  | 小王
2  | 小宋
3  | 小白
小王 | 10
小宋 | 22
小肖 | 31

列标题来自于第一个查询的字段名称。

##### 15.2.2 UNION ALL

UNION ALL操作符可以组合两个SELECT语句的结果，并且**包含重复的结果**。其使用规则于UNION相同。

基本语法如下：

    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]
    UNION ALL
    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]

示例：

    SELECT id, NAME, age FROM t1 
    UNION ALL 
    SELECT id, NAME, age FROM t2;

返回数据：

id | NAME  |  age
-----|-----|-----
1 |  小王 | 10
2 |  小宋 | 20
3 |  小白 | 30
1 |  小王 | 10
2 |  小宋 | 22
3 |  小肖 | 31

*从效率上说，UNION ALL 要比UNION快很多，所以，如果可以确认合并的两个结果集中不包含重复数据且不需要排序时的话，那么就使用UNION ALL。*

##### 15.2.3 INTERSECT - - 交集（MySQL不支持）

INTERSECT可以组合两个SELECT语句，但只返回第一个SELECT语句里与第二个语句里一样的记录。其使用规则与UNION操作符相同。

    SELECT PROD_DESC FROM products_tbl 
    INTERSECT 
    SELECT LAST_NAME FROM employee_tbl;

对t1和t2取交集

示例：

    -- 使用DISTINCT运算符和INNER JOIN子句，USING()表示内连接的两个表的对应字段相等
    SELECT DISTINCT id, NAME, age FROM t1 INNER JOIN t2 USING(id, NAME, age);
    -- 使用UNION
    SELECT  id,  NAME,  age, COUNT(*)  
    FROM (SELECT id, NAME, age  
        FROM t1  
        UNION ALL  
        SELECT id, NAME, age  
        FROM t2  
        ) tmp -- 这里必须给这个查询起一个别名  
    GROUP BY id, NAME, age  
    HAVING COUNT(*) > 1  

返回数据：

id | name  |  age
-----|-----|-----
1  | 小王 | 10

##### 15.2.4 EXCEPT - - 差集（MySQL不支持）

EXCEPT操作符组合两个SELECT语句，返回第一个SELECT语句里有但第二个SELECT语句里没有的记录。

Oracle使用MINUS操作符，SQL Server使用EXCEPT操作符。

基本语法如下：

    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]
    EXCEPT
    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]

下面t1对t2求差集：

    -- 使用子查询NOT IN
    SELECT id, NAME, age FROM t1 
    WHERE t1.name NOT IN (SELECT NAME FROM t2) 
    OR 
    t1.age NOT IN (SELECT age FROM t2);
    -- 使用左外连接
    SELECT t1.id, t1.name, t1.age 
    FROM t1 LEFT JOIN t2 ON 
    t1.id = t2.id 
    WHERE t1.name <> t2.name OR t1.age <> t2.age;

返回结果：

id | name  |  age
----- | ----- | -----
2 |  小宋 | 20
3 |  小白 | 30

#### 在组合查询中使用ORDER BY

ORDER BY子句可以用于组合查询，但只能用于对全部查询结果的排序，因此组合语句里虽然可以包含多个查询，但只能有一个ORDER BY子句，并且**它只能以别名或数字来引用字段。**

语法如下：

    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]
    OPERATOR
    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]
    [ORDER BY]

示例：

    SELECT EMP_ID FROM employee_tbl 
    UNION 
    SELECT EMP_ID FROM employee_pay_tbl 
    ORDER BY 1;

返回数据：

<table>
    <tr>
        <td bgcolor=silver class='medium'>EMP_ID</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>AE0021</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>AX2911</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>BR1122</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>EF2120</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>IP0992</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>MI8837</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>NU3451</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>YB0928</td>
    </tr>
</table>

#### 15.4 组合查询里使用GROUP BY

与ORDER BY不同的是，GROUP BY子句可以用于组合查询中的每一个SELECT语句，也可以用于全部查询结果。另外HAVING子句也可以用于组合查询里的每个SELECT语句。

语法如下：

    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]
    OPERATOR
    SELECT COLUMN1 [, COLUMN]
    FROM TABLE1 [, TABLE2]
    [WHERE]
    [GROUP BY]
    [HAVING]
    [ORDER BY]

示例：

    SELECT 'EMPLOYEES' TYPE, COUNT(*) FROM employee_tbl 
    UNION 
    SELECT 'PRODUCTS' TYPE, COUNT(*) FROM products_tbl 
    GROUP BY 1
    ORDER BY 2 DESC;

这个SQL语句返回表employee_tbl和表products_tbl各自的记录数。

返回数据：

TYPE  |  COUNT(*)
-----|-----
PRODUCTS  |  11
EMPLOYEES |  8

#### 15.5 获取准确的数据

使用组合查询时要小心。在使用“交集”操作时，如果第一个查询的SELECT语句有问题就可能会得到不正确或不完整的数据。另外在使用UNION及UNION ALL时要考虑是否需要返回重复的数据。对于“差集”操作，我们要考虑是否需要不存在于第二个查询里的数据。

### 第16章 利用索引改善性能

本章介绍如何通过创建和使用索引来改善SQL语句的性能。

#### 16.1 什么是索引

简单来说，索引就是一个指针，指向表里的数据。索引在数据库里起到指向数据在表里准确物理位置的作用。实际上，我们被引导到数据在数据库底层文件里的位置，但从表面看，我们是在引用一个表。

索引通常于相应的表是分开保存的，其主要目的是提高数据检索的性能。索引的创建与删除不会影响到数据本身，但会影响数据检索的速度。索引也会占据物理存储空间，而且可能会比表本身还大。因此在考虑数据库的存储空间时，需要考虑索引要占用的空间。

#### 16.2 索引是如何工作的

索引在创建之后，用于记录与被索引字段相关联的位置值。当表里添加新数据时，索引也会添加新项。当数据库执行查询，而且WHERE条件里指定的字段已经设置了索引时，数据库会首先在索引里搜索WHERE子句里指定的值。如果在索引中找到了这个值，索引就可以返回被搜索数据在表里的实际位置。

假设有以下索引：

数据 | 位置
-----|-----
GLASS | 6
JONES | 2
JONES | 9
PLEW | 5
SMITH | 1
SMITH | 3
SMITH | 7
SMITH | 100000
WALLACE | 8
WALLACE | 4
... | ...

该索引对应的表：

位置 | 数据
----- | -----
1 | SMITH
2 | JONES
3 | SMITH
4 | WILLIAMS
5 | PLEW
6 | GLASS
7 | SMITH
8 | JONES
... | ...
100000 | SMITH

假设此时进行以下查询：

    SELECT * FROM TABLE_NAME WHERE NAME = 'SMITH';

此时数据库会引用索引NAME来寻找“SMITH”的位置；在找到了位置后，数据就能迅速地从表里检索出来。在索引里，数据是按照字母顺序排序的。

如果表里没有索引，在执行相同的查询时，数据库就会进行全表扫描，也就是说表里的每行数据都会被读取来对NAME字段的值进行判断。

索引通常以一种树形结构保存信息，因此速度比较快。假设我们对一个书名列表设置了索引，这个索引具有一个根节点，也就是每个查询的起始点。根节点具有分支，在本例中可以有两个分支，一个代表字母A~L，另一个代表M~Z。如果要查询以P为开头的书名，我们就会从根节点进入索引，然后迅速跳转到包含字母M~Z的分支。

#### 16.3 CREATE INDEX命令

与其他语句相同，创建索引的语句在不同的实现中也是不同的。多数实现使用CREATE INDEX语句：

    CREATE INDEX INDEX_NAME ON TABLE_NAME

不同厂商的CREATE INDEX语句在选项方面有不少差别，有些实现允许指定存储子句、允许排序、允许使用簇。

#### 16.4 索引的类型

数据库里的表可以创建多种类型的索引，它们的目标是一样的：通过提高数据检索速度来改善数据库性能。

##### 16.4.1 单字段索引

对单个字段的索引是索引中最简单、最常见的形式。

语法如下：

    CREATE INDEX INDEX_NAME 
    ON TABLE_NAME (COLUMN_NAME)

*如果某个字段经常在WHERE子句中作为单独的查询条件，它的单字段索引是最有效的。适合作为单字段索引的值有ID、序列号或系统指派的键值等。*

示例：对employee_tbl中的EMP_ID创建索引

    CREATE INDEX emp_id_index ON employee_tbl (EMP_ID);

##### 16.4.2 唯一索引

唯一索引用于改善性能和保证数据完整性。唯一索引不允许表里具有重复值，除此之外，它与普通索引的功能一样。

语法如下：

    CREATE UNIQUE INDEX INDEX_NAME 
    ON TABLE_NAME (COLUMN_NAME)

示例：

    CREATE UNIQUE INDEX emp_id_index ON employee_tbl (EMP_ID);

##### 16.4.3 组合索引

组合索引是基于一个表里两个或多个字段的索引。在创建组合索引时，需要考虑性能问题，因为字段在索引里的次序对数据检索速度有很大的影响。一般来说，最具有限制的值应该排在前面，从而获得最好的性能。但是，总在查询里指定的字段应该放在首位。

语法如下：

    CREATE INDEX INDEX_NAME 
    ON TABLE_NAME (COLUMN_NAME1, COLUMN_NAME2)

示例：

    CREATE INDEX ORD_IDX
    ON ORDERS_TBL (CUST_ID, PROD_ID);

*对于经常在查询的WHERE子句里共同使用的字段，组合索引是最有效的。*

##### 16.4.4 隐含索引

隐含索引是数据库服务程序在创建对象时自动创建的。比如，数据库会为主键约束和唯一性约束自动创建索引。

#### 16.5 何时考虑使用索引

唯一索引隐含地与主键共同实现主键的功能。外键经常与父表结合，所以也适合设置索引。一般来说，大多数用于表结合的字段都应该设置索引。

经常在ORDER BY和GROUP BY里引用的字段也应该考虑设置索引。举例来说，如果根据个人姓名进行排序，对姓名字段设置索引会大有好处。它会对每个姓名自动按字母顺序进行排序，简化了实际的白须操作，提高了输出结果的速度。

具有大量唯一值的字段，或是在WHERE子句里会返回很小部分记录的字段，都可以考虑设置索引。这主要是为了测试或避免错误。索引在投入使用前应该需要反复进行测试。同时我们用一些时间来常使不同的索引组合。

#### 16.6 何时应该避免使用索引

- 索引不应该用于小规模的表。因为查询索引会增加额外的查询时间。对于小规模的表，让搜索引擎进行全表扫描往往更快。
- 当字段用于WHERE子句作为过滤器会返回表里的大部分记录时。该字段就不适合设置索引。
- 经常会被批量更新的表可以具有索引，但批量操作的性能会由于索引而降低。对于经常会被加载或批量操作的表来说，可以在执行批量操作之前去除索引，在完成操作后再重新创建索引。这是因为当表里插入数据时，索引也会被更新，从而增加了额外的开销。
- 不应该对包含大量NULL值的字段设置索引。字段中过多的NULL值会严重影响索引的运行效率。
- 经常被操作的字段不应该设置索引，因为对索引的维护会变得很繁重。

举例来说，现有以下查询：

    SELECT * FROM TABLE_NAME
    WHERE GENDER = 'FEMALE';

在运行上述查询时，表与索引之间有一个持续的行为。由于该WHERE子句会返回大量记录，数据库服务程序必须持续地读取索引、然后读取表的内容、再读取索引、再读取表，如此反复。这回导致效率的降低。由于该表的大部分数据肯定是要被读出的，所以使用全表扫描可能会效率更高。

*另外，对于特别长的关键字创建索引时要十分谨慎，大量的I/O开销会不可避免地降低数据库的性能。*

#### 16.7 修改索引

创建索引后也可以进行更改。

语法如下：

    ALTER INDEX INDEX_NAME ...

对生产系统进行修改时需要小心。大部分情况下，对索引进行修改会被马上执行，引起系统资源的额外开销。

#### 16.8 删除索引

语法如下：

    DROP INDEX INDEX_NAME

MySQL中需要指明表：

    DROP INDEX INDEX_NAME ON TABLE_NAME;

示例：

    DROP INDEX EMP_ID_INDEX ON employee_tbl;

删除索引最常见的原因是尝试改善性能。


### 第17章 改善数据库性能

本章介绍一些非常简单的方法调整SQL语句来获得更好的性能。

#### 17.1 什么是SQL语句调整

SQL语句调整是优化生成SQL语句的过程，从而以最有效和最高效的方式获得结果。首先是查询里元素的基本安排，因为简单的格式化过程就能在语句优化中发挥很大作用。

SQL语句调整主要设计调整语句的FROM和WHERE子句，因为数据库服务程序主要根据这两个子句执行查询。

#### 17.2 数据库调整与SQL语句调整

首先我们需要先理解数据库调整与SQL语句调整之间的差别。

数据库调整是调整实际数据库的过程，包括分配内粗呢、磁盘、CPU、I/O和底层数据库进程，还涉及数据库结构本身的管理与操作，比如表和索引的设计与布局。另外，数据库调整通常会包括调整数据库体系来优化硬件的使用。**数据库调整的目标是确保数据库设计能够最好地满足用户对数据库操作的需要。**

SQL语句调整是调整访问数据库的SQL语句，这些语句包括数据库查询和事务操作，比如插入、更新和删除。**SQL语句调整的目标是利用数据库和系统资源、索引，针对数据库当前状态进行最有效的访问，减少对数据库执行查询所产生的开销。**

为了访问数据库时达到最优结果，数据库调整和SQL语句调整都需要进行。一个调整很差的数据库会极大地抵消SQL调整所付出的努力，反之亦然。理想状态下，最好首先调整数据库，确保必要的字段都具有索引，然后再调整SQL代码。

#### 17.3 格式化SQL语句

格式化SQL将分成以下小节进行讨论：

- 为提高可读性格式化SQL语句
- FROM子句里表的顺序
- 最严格条件在WHERE子句里的位置
- 结合条件在WHERE子句里的位置

*
大多数关系型数据库实现里有一个名为“SQL优化器”的东西，它可以执行SQL语句，并且基于SQL语句的构成方式和数据库里可用的索引来判断执行语句的最佳方式。不同的实现优化器通常不同。
*


##### 17.3.1 为提高可读性格式化SQL语句

为提高可读性格式化SQL语句是件很显然的事情。虽然语句的整洁程度并不会影响实际的性能，单仔细地使用格式是调整语句的第一步。

让语句具有良好的可读性的基本规则如下：

- 每个子句都以新行开始
- 当子句里的参数超过一行长度需要换行时，使用制表符或空格来形成缩进
- 以一致的方式使用制表符和空格
- 当语句里使用多个表时，使用表的别名
- 如果SQL实现中允许使用注释，应当在语句里有节制的使用注释
- 如果要在SELECT语句里使用多个字段，就让每个字段都从新行开始
- 如果在FROM子句里要使用多个表，就让每个表名都从新行开始
- 让WHERE子句里每个条件都以新行开始

示例：以下是一个可读性极差的SQL语句

    SELECT customer_tbl.CUST_ID, customer_tbl.CUST_NAME,
    customer_tbl.CUST_PHONE, orders_tbl.ORD_NUM, orders_tbl.QTY
    FROM customer_tbl, orders_tbl
    WHERE customer_tbl.CUST_ID = orders_tbl.CUST_ID
    AND orders_tbl.QTY > 1 AND customer_tbl.CUST_NAME LIKE 'G%'
    ORDER BY customer_tbl.CUST_NAME;

格式化后的SQL语句：

    SELECT C.CUST_ID,
           C.CUST_NAME,
           C.CUST_PHONE, 
           O.ORD_NUM, 
           O.QTY
    FROM customer_tbl C,
         orders_tbl O
    WHERE C.CUST_ID = O.CUST_ID
          AND O.QTY > 1 
          AND C.CUST_NAME LIKE 'G%'
    ORDER BY 2;

这两个语句的效果是完全一样的，单第二个语句具有更好的可读性。

##### 17.3.2 FROM子句里的表

FROM子句里表的安排或次序对性能有很大影响，这取决于优化器如何读取SQL语句。通常来说把较小的表列在前面，把较大的表列在后面，可以获得更好的性能。

##### 17.3.3 结合条件的次序

大多数结合使用一个基表连接到具有一个或多个共有字段的其他表。基表是主表，查询里的大多数或全部表都与它结合。在WHERE子句里，来自基表的字段一般放在结合操作的右侧，要被结合的表通常按照从小到大的次序排序。

如果没有基表，那表就应该从小到大排列，让最大的表位于WHERE子句里结合操作的右侧。结合条件应该位于WHERE子句的最前面，其后才是过滤条件。

示例：

    FROM  TABLE1 t1,            -- Smallest table
          TABLE2 t2,            -- to
          TABLE3 t3             -- Largest table, also base table
    WHERE t1.column = t3.column -- Join condition
    AND   t2.column = t3.column -- Join condition
    [AND CONDITION1]            -- Filter condition
    [AND CONDITION2]            -- Filter condition

##### 17.3.4 最严格条件

最严格条件通常是SQL查询达到最优性能的关键因素。最严格条件是WHERE子句里返回最少记录的条件。这里我们重点关注最严格条件，因为它对查询返回的数据进行了最大限度的过滤。

我们应该让SQL优化器首先计算最严格条件，因为它会返回最小的数据集，从而减小查询的开销。

通常的SQL实现先读取WHERE子句的底部条件，因此需要把最严格条件放到WHERE子句的末尾。

示例：

    FROM  TABLE1 t1,            -- Smallest table
          TABLE2 t2,            -- to
          TABLE3 t3             -- Largest table, also base table
    WHERE t1.column = t3.column -- Join condition
    AND   t2.column = t3.column -- Join condition
    [AND CONDITION1]            -- Least restrictive
    [AND CONDITION2]            -- Most restrictive

#### 17.4 全表扫描

在没有使用索引时，或是SQL语句所使用的表没有索引时，就会发生全表扫描。一般来说，全表扫描返回数据的速度明显比使用索引慢。表越大，全表扫描返回数据对的速度就越慢。查询优化器会决定在执行SQL语句时是否使用索引，而大多数情况会使用索引，如果索引存在的话。

在读取大规模的表时应该避免全表扫描。

#### 17.5 其他性能考虑

在调整SQL语句里还有其他的一些性能考虑：

- 使用LIKE操作符和通配符
- 避免OR操作符
- 避免HAVING子句
- 避免大规模排序操作
- 使用存储过程
- 在批加载时关闭索引

##### 17.5.1 使用LIKE操作符和通配符

LIKE操作符是很有用的工具，它能灵活地为查询设置条件。在查询里使用通配符能够消除很多可能返回的记录。

假设有以下查询：

    -- 查询 1
    SELECT EMP_ID, FIST_NAME, LAST_NAME, CITY 
    FORM employee_tbl
    WHERE LAST_NAME LIKE 'STEVENS';
    -- 查询 2
    SELECT EMP_ID, FIST_NAME, LAST_NAME, CITY 
    FORM employee_tbl
    WHERE LAST_NAME LIKE '%EVENS%';
    -- 查询 3
    SELECT EMP_ID, FIST_NAME, LAST_NAME, CITY 
    FORM employee_tbl
    WHERE LAST_NAME LIKE 'ST%';

这些SQL语句并不返回同样的结果。这里通常来说查询2应该比查询3快，因为查询3确定搜索字符串的开头字符，因此它能够利用索引。

##### 17.5.2 避免使用OR操作符

在SQL语句里使用IN代替OR操作符能够提高数据检索速度，

示例：

    SELECT EMP_ID, FIRST_NAME, LAST_NAME
    FROM employee_tbl 
    WHERE CITY = 'INDIANAPOLIS'
    OR    CITY = 'D.C.'
    OR    CITY = 'GREENFIELD';

下面是同一个查询，不过使用了IN：

    SELECT EMP_ID, FIRST_NAME, LAST_NAME
    FROM employee_tbl 
    WHERE CITY IN ('INDIANAPOLIS', 'D.C.', 'GREENFIELD');

这两个查询中后者查询效率较高。

##### 17.5.3 避免使用HAVING子句

HAVING子句是很有用的，可以减少GROUP BY子句返回的数据数目，但它的使用也要付出代价。HAVING子句会让SQL优化器进行额外的工作，这也就需要额外的时间。这样的查询纪要对返回的结果集进行分组，又要根据HAVING子句限制的条件对结果集进行分析。

在可能的情况下尽量不要在SQL语句中使用HAVING子句，如果需要使用，则最好尽可能地使其中的限制条件简单化。

##### 17.5.4 避免大规模排序操作

大规模排序操作意味着使用ORDER BY、GROUP BY和HAVING子句。排序的主要问题是会影响SQL语句的响应时间。

##### 17.5.5 使用存储过程

我们可以为经常运行的SQL语句创建存储过程。存储过程就是经过编译的、以可执行格式永久保存在数据库里的SQL语句。

一般情况下，当SQL语句被提交给数据库时，数据库必须检查它的语法，并且把语法转化为可以在数据库里成型的格式。语句被解析之后就保存在内存中，但这并不是持久的。也就是说，当其他操作需要内存时，语句就会被从内存里释放。而在使用存储过程时，SQL语句总是处于可执行格式，并且一致会保存在数据库里，直到像别的数据库对象一样被删除。

##### 17.5.6 在批加载时关闭索引

当用户向数据库提交一个事务时（INSERT、UPDATE或DELETE），表和这个表相关联的索引里都会有数据变化。即用户更新了一个表时，其相关索引也会改变。

当数据规模较大时这回带来严重的性能问题。

所以在批加载前应该去除索引，在批处理完毕后再重建索引。

在批加载操作的i去拿后删除并重建索引还有另一个优点，就是可以减少索引里的碎片。
当数据库不断增长，记录被添加、删除和更新时就会产生碎片。对于不断增长的数据库来说，最好定期地删除和重建索引。当索引被重建时，构成索引的物理空间数量减少了，也就减少了读取索引所需的磁盘I/O，用户就会更快地得到结果。

## 第六部分 使用SQL管理用户和安全

### 第18章 管理数据库用户 

本章介绍关系型数据库里一个最关键的管理功能：管理数据库用户。该功能可以确保指定用户和应用对数据库的访问，并拒绝非指定的外部访问。

#### 18.1 数据库的用户管理

用户是我们所做一切工作的原因：设计、创建、实现和维护数据库。在数据库设计时就考虑了用户的需求，而实现数据库的最终目标是把它交给用户，让用户使用。

用户管理是关系型数据库生存周期内一件非常重要的工作，它最终是通过使用SQL概念和命令来实现的。对于数据库管理员来说，用户管理的最终目标是在让用户访问所需的数据与保持数据完整性之间寻求平衡。

##### 18.1.1 用户的类型

数据库用户的类型有多种，包括：

- 数据输入源
- 程序员
- 系统工程师
- 数据库管理员
- 系统分析员
- 开发人员
- 测试人员
- 管理者
- 终端用户

每种用户在数据库里具有不同的权限级别。

##### 18.1.2 谁管理用户

数据库管理员或其他被指定的人负责管理数据库中的用户。

数据管理员（DBA）通常负责创建数据库用户账户、角色、权限和特征，以及响应的删除操作。在大型使用环境中，这可能是非常繁重的工作，有些公司会安排一个安全员协助DBA进行用户管理。

安全员负责文书工作，向DBA传递用户的工作需求，让DBA直到哪些用户不再需要访问数据库了。
系统分析员或系统管理员通常负责操作系统安全，包括创建用户和分配适当的权限。

##### 18.1.3 用户在数据库里的位置

用户需要被赋予一定的角色和权限才能完成自己的工作，但用户的权限也不能超出其工作范围。设置用户账户和安全的唯一也是全部原因就是保护数据。如果错误的用户访问了错误的数据，即使是在无意情况下，数据也可能会被毁坏或丢失。当用户不再需要访问数据库时，相应的账户应尽快从数据库里删除或禁止。

##### 18.1.4 不同规划里的用户

数据库对象与数据库用户账户相关联，称为规划。规划是数据库用户拥有的数据库对象集，这个用户被称为规划所有人。规划在逻辑上的组织类似于数据库中的对象，由一个特定的所有人进行管理。

例如，可以将所有的人事表组织起来成为一个名HR的规划，便于进行人力资源管理。普通数据库用户与规划所有人之间的区别在于后者在数据库里拥有对象，而大多数用户没有自己的对象，只是被赋予数据库账户来访问规划里的数据。由于规划所有人实际上拥有这些对象，所以对它们有完全的控制。

#### 18.2 管理过程

在任何数据库系统中，一个稳定的用户管理系统对于数据安全来说是必不可少的。用户管理系统从新用户的直接上级开始，他负责发起访问请求，然后就是通过公司的批准程序。如果管理层接受了请求，就会转到安全员或数据库管理员来完成实际操作。一个好的通知过程是必要的，在用户账户被创建、对数据库的访问被批准之后，管理人和用户必须得到通知，用户账户的密码应该只交给用户本人，而他应该在第一次登录到数据库后就立即修改密码。

##### 18.2.1 创建用户

创建数据库用户需要使用数据库里的SQL命令，但并不存在什么标准命令，每个实现都有自己的方法。不同的实现中基本概念都是一样的。另外也可以使用一些图形化用户界面工具进行用户管理。

在MySQL中创建用户：

1. 在数据库里创建用户账户
2. 给用户账户分配适当的权限

创建用户账户的语法：

    SELECT USER user [IDENTIFIED BY [PASSWORD] 'password'];

分配用户权限的语法：

    GRANT priv_type [(column_list)] [, priv_type [(column_list)]] ...
    ON [object_type]
    {tbl_name | * | *.* | db_name.* | db_name.routine_name}
    TO user;

##### 18.2.2 创建规划

规划是使用CREATE SCHEMA语句创建的。

语法如下：

    CREATE SCHEMA [SCHEMA_NAME] [USER_ID]
                  [DEFAULT CHARACTER SET CHARACTER_SET]
                  [PATH SCHEMA NAME [, SCHEMA NAME]]
                  [SCHEMA_ELEMENT_LIST]

##### 18.2.3 删除规划

使用DROP SCHEMA语句可以从数据库里删除规划，这时必须要考虑两个选项。一个是RESTRICT，在使用这个选项时，如果规划里有对象，删除操作就会发生错误。第二个选项是CASCADE，如果规划里有对象，删除规划就必须指定这个选项。

*当我们删除规划时，与规划相关联的全部数据库对象都会被删除。*

语法如下：

    DROP SCHEMA SCHEMA_NAME {RESTRICT | CASCADE}

##### 18.2.4 调整用户

用户管理中的一个重要组成部分是在创建用户之后修改用户的属性。

不同实现中语法不同。

MySQL调整用户：

    -- 重置用户的密码
    UPDATE mysql.user 
    SET Password = PASSWORD('new password') 
    WHERE USER = 'username';
    -- 改变用户的用户名
    RENAME USER old_username TO new_username;
    ...

##### 18.2.5 用户会话

一个用户数据库会话就是从登录到退出这段时间。在一个用户会话中，用户可以执行允许范围内的各种操作。

使用下面的命令可以明确地连接和断开数据库，从而开始和结束SQL会话：

    CONNECT TO DEFAULT | STRING1 [AS STRING2] [USER STRING3]
    DISCONNECT DEFAULT | CURRENT | ALL | STRING 
    SET CONNECT DEFAULT | STRING

##### 18.2.6 禁止用户访问

通过几个简单的命令就可以从数据库里删除用户或禁止用户的访问，但在不同实现里具体的命令依然是不同的。

下面是禁止用户访问数据库的一些方法：

- 修改用户的密码
- 从数据库删除用户账户
- 撤销分配给用户的相应权限

在很多实现里，与GRANT命令执行相反操作的是REVOKE，用于取消已经分配给用户的权限。

    REVOKE PRIV1 [, PRIV2, ...] FRO, USERNAME

### 第19章 管理数据库安全

本章介绍使用SQL命令和相关命令在关系型数据库里实现和管理安全的基本只是。不同实现的安全命令语法上是有区别的，但关系型数据库的整体安全概念都遵循ANSI标准。

#### 19.1 什么是数据库安全

数据库安全就是保护数据不受未授权访问。这种保护还包括防止未授权的连接和权限分配。

数据库里存在多个用户级别，从数据库创建者，到负责维护数据库的人员，到数据库程序员，到终端用户。终端用户虽然在访问权限上受到最大的限制，但却是数据库存在的原因。每个用户对数据库具有不同的访问级别，应该被限制到能够完成相应工作所需的最小权限。

良好规划和维护用户管理与数据库整体安全是密切相关的。用户被分配一定的账户和密码，从而可以对数据库进行一般的访问。如果用户不再需要一定的权限，那么这些权限应该被撤销。如果用户不再需要访问数据库，用户账户就应该从数据库里删除。

一般来说，用户管理是创建用户账户、删除用户账户、跟踪用户在数据库里行为的过程。而数据库安全更进一步，包括了为特定数据库访问授予权限、从用户撤销权限、采取手段保护数据库里的其他部分。

#### 19.2 什么是权限

权限是用于访问数据库本身、访问数据库里的对象、操作数据库里的数据、在数据库里执行各种管理功能的许可级别。权限通过GRANT命令分配，通过REVOKE命令撤销。

用户可以连接到数据库并不意味着可以访问数据库里的数据，要访问数据还哭里的数据还需要权限。权限有两种类型：系统权限和对象权限。

##### 19.2.1 系统权限

系统权限允许用户在数据库里执行管理操作，比如创建数据库、删除数据库、创建用户账户、删除用户、删除和修改数据库对象、修改对象的状态、修改数据库的状态以及其他会对数据库造成重要影响的操作。

MySQL里一些常见的全局（系统）权限：

- CREATE：允许用户创建特定对象，如数据库、表或索引
- DROP：允许用户删除特定对象
- GRANT：允许用户对特定对象分配权限
- RELOAD：允许用户进行清除缓存操作，以便清除缓存中的日志文件等内容
- SHUTDOWN：允许用户关闭MySQL实例

##### 19.2.2 对象权限

对象权限是针对对象的许可级别，意味着必须具有适当的权限才能对数据库对象进行操作。例如，为了从其他用户的表里选择数据，我们必须首先得到另一个用户的许可。**对象权限由对象的所有者授予数据库里的其他用户。**这个所有者也被称为规划所有者。

ANSI标准里包含下述对象权限：

- USAGE：批准使用指定的域
- SELECT：允许访问指定的表
- INSERT(COLUMN_NAME)：允许对数据插入到指定表的指定字段
- INSERT：允许对数据插入到指定表的全部字段
- UPDATE(COLUMN_NAME)：允许对指定表里的指定字段进行更新
- UPDATE：允许对指定表里的全部字段进行更新
- REFERENCES(COLUMN_NAME)：允许在完整性约束里引用指定表里的指定字段，任何完整性约束都需要这个权限
- REFERENCES：允许引用指定表里的全部字段

*对象的所有者自动被授予对象相关的全部权限。*

大多数SQL实现都遵循这个对象权限列表来控制对数据库对象的访问。
这些对象级别的权限应该用于许可和限制对规划内的对象的访问，可以保护一个规划里的对象不被能够访问其他规划的用户访问。

*不同SQL实现里还有一些对象权限没有列出，需要查看具体实现的文档。*

##### 19.2.3 谁负责授予和撤销权限

使用GRANT和REVOKE命令的人通常是DBA。具体要授予和撤销的权限来自于管理层，而且最好进行细致的跟踪，以确保只有被认可的用户才能具有相应的权限。

对象的所有者负责向数据库里的其他用户授予权限，即使DBA也不能给数据库用户授予不属于他的对象的权限，虽然由方法可以绕过这种限制。

#### 19.3 控制用户访问

用户访问主要是通过用户账户和密码进行控制的，但在大多数主流实现里，这是不足以访问数据库的。创建用户账户只是允许和控制数据库访问的第一步。

在创建了数据库账户之后，DBA、安全员或某个指定的人必须能够向需要进行数据库操作的用户授予适当的系统级权限，比如创建表或选择表。接下来，规划所有者需要向用户授予访问规划中对象的权限。

##### 19.3.1 GRANT命令

GRANT命令用于向现有数据库用户账户授予系统级和对象级权限。

语法如下：

    GRANT PRIVILEGE1 [, PRIVILEGE2] [ON OBJECT]
    TO USERNAME [WITH GRANT OPTION | ADMIN OPTION]

示例：

    -- 对USER1授予对表employee_tbl查询的权限
    GRANT SELECT ON employee_tbl TO USER1;
    -- 授予多个权限
    GRANT SELECT, INSERT ON employee_tbl TO USER1;
    -- 对多个用户授予多个权限
    GRANT SELECT, INSERT ON employee_tbl TO USER1, USER2;

**
GRAND OPTION
**

GRAND OPTION是个功能强大的GRANT选项。当对象的所有者利用GRANT OPTION把自己对象的权限授予另一个用户时，这个用户还可以把这个对象的权限授予其他用户，尽管他并不是这个对象的所有者。

示例：

    GRANT SELECT ON employee_tbl TO USER1 GRANT OPTION;

**
ADMIN OPTION
**

使用ADMIN OPTION授予权限之后，用户不仅拥有了权限，也就有了把这个权限授予其他用户的能力，这一点域GRANT OPTION类似。但GRANT OPTION用于对象级权限，而ADMIN OPTION用于系统级权限。

示例：

    GRANT CREATE TABLE TO USER1 WITH ADMIN OPTION;

##### 19.3.2 REVOKE命令

REVOKE命令撤销已经分配给用户的权限，它有两个选项RESTRICT和CASCADE。当使用RESTRICT选项时，只有当REVOKE命令里指定的权限撤销后不会导致其他用户产生权限报废时，REVOKE才能顺利完成。而CASCADE会撤销权限，不会一流其他用户的权限。

举例来说，如果某个对象的所有者使用GRANT OPTION把权限授予USER1，USER1又使用了GRANT OPTION将权限授予USER2，然后对象所有者撤销了USER1的权限，这时如果使用CASCADE选项，那么USER2的权限也会被撤销。

当用户使用GRANT OPTION向其他用户授予权限之后，自己被从数据库里删除了，或是自己的权限被撤销了，那么后一个用户的权限就被称为报废权限。

REVOKE语法如下：

    REVOKE PRIVILEGE1 [, PRIVILEGE2] [GRANT OPTION FOR] ON OBJECT 
    FROM USER {RESTRICT | CASCADE}

示例：

    REVOKE INSERT ON employee_tbl FROM USER1;

##### 19.3.3 控制对单独字段的访问

我们不仅能够把表作为一个整体来分配对象权限（INSERT、UPDATE和DELETE），还可以分配表里指定字段的权限来限制用户访问，如下所示：

    GRANT UPDATE(NAME) ON employee_tbl TO USER1;

##### 19.3.4 数据库账户PUBLIC

数据库账户PUBLIC是代表数据库里全体用户的账户。所有用户都属于PUBLIC账户，如果把某个权限授予PUBLCI账户，那么数据库全部用户都具有这个权限。类似地，如果一个权限在PUBLIC账户上被撤销，就相当于从全部数据库用户上撤销了这个权限。

示例：

    GRANT SELECT ON employee_tbl TO PUBLIC;

##### 19.3.5 权限组

有些实现可以在数据库里形成权限组。这些权限组是通过不同的名称来引用的。通过使用权限组，我们可以更方便地给用户授予和撤销权限。

大多数SQL实现中已经实现了权限组。

#### 19.4 通过角色控制权限

角色是数据库里的一个对象，具有类似权限组的特性。通过使用角色，我们不必明确地直接给用户授予权限，从而减少安全维护工作。使用角色可以更方便地进行组权限管理。

当一个角色最初被创建时，他就是数据库里的一个角色，没有任何实际值。角色可以被指派给用户或其他角色。假设在名为APP01的规划里，我们把对employee_tbl的SELECT权限授予角色RECORDS_CLERK，那么任何被指派了RECORDS_CLERK的角色的用户或角色就对表employee_tbl具有了SELECT权限。

##### 19.4.1 CREATE ROLE语句

角色是通过CREATE ROLE创建的：

    CREATE ROLE role_name;

示例：向角色授予权限，将角色指派给用户

    CREATE ROLE RECORDS_CLERK;
    GRANT SELECT, INSERT, UPDATE, DELETE ON employee_tbl TO RECORDS_CLERK;
    GRANT RECORDS_CLERK TO USER1;

##### 19.4.2 DROP ROLE语句

该语句用于删除角色。

语法：

    DROP ROLE role_name;

示例：

    DROP ROLE RECORDS_CLERK;

##### 19.4.3 SET ROLE语句

使用SET ROLE语句可以为用户的SQL会话设置角色：

    SET ROLE role_name;

示例：

    SET ROLE RECORD_CLERL;

可以在一个与距离设置多个角色：

    SET ROLE ROLE1, ROLE2, ...

## 第七部分 摘要数据结构

### 第20章 创建和使用视图及异名

本章介绍关于性能的一些问题，以及如何创建和删除视图，如何把视图用于安全管理，如何简化终端用户和报告的数据获取，最后还会讨论异名。

#### 20.1 什么是视图

**视图是一个虚拟表。**也就是说，对于用户来说，视图在外观和行为上都类似于表，但它不需要实际的物理存储。视图实际上是由预定义查询形式的表所组成。视图可以包含表的全部或部分记录，可以由一个表或多个表创建。

当创建一个视图时，实际上是在数据库里执行了一个SELECT语句，它定义了这个视图。这个SELECT语句可能只包含表里的字段名称，也可以包含各种函数和运算来操作或汇总给用户显示的数据。

虽然视图并不占据实际的存储空间，但也被看作是一个数据库对象。视图与表之间的主要区别在于，表占据物理空间，而视图不需要物理空间，它只是从表里引用数据。

在数据库中，视图的使用方式与表是一样的，意味着我们可以像操作表一样从视图里获取数据。另外，我们还可以对视图里的数据进行操作，但存在着一定的限制。

##### 20.1.1 使用视图来简化数据访问

在有些情况下，通过数据库归一化，或是数据库设计过程，数据在表里的格式可能并不适合终端用户进行查询。这时，我们可以创建一系列视图，让终端用户能够更简单地进行查询。

*如果用于创建视图的表被删除了，那么这个视图就不可访问了。如果对这个视图进行查询，会发生报错。*

##### 20.1.2 使用视图作为一种安全形式

使用视图可以限制用户只访问表里的特定字段或满足一定条件的记录（在定义视图的WHERE子句里指定）。

视图可以作为数据库里的一种安全形式。

假设我们有一个表employee_tbl，它包含雇员姓名、地址、电话、紧急联系人、部门、职位、薪水或小时工资。在编写一些报告时可能会有一些临时需求，例如要获取雇员的姓名、地址和电话号码。如果允许访问整个表，别人就会看到每个雇员的收入是多少，这是我们不允许的。为了防止这种情况的发生，我们可以创建一个视图，让它只包含必要的信息：雇员姓名、地址和电话，并且让写报告的人可以使用这个视图，这样就可以避免他们访问表的其他敏感数据。

##### 20.1.3 使用视图维护摘要数据

假如摘要数据报告所基于的表经常发生更新，或是报告经常被创建，使用视图来包含摘要数据就是个很好的选择。

利用摘要数据创建视图与从一个表或多个表创建视图的唯一区别就是使用了汇总函数。

视图只保存在内存里，而且只需要保存其定义本身，这一点与其他数据库对象不同。视图由创建者或规划所有者所拥有。视图所有者自动拥有视图的全部权限，并且可以把视图的权限授予其他用户。对于视图而言，GRANT命令的GRANT OPTION权限的工作方式与表一样。

#### 20.2 创建视图

视图是通过CREATE VIEW语句创建的。我们可以从一个表、多个表或另一个视图来创建视图。为了创建视图，用户必须拥有适当的系统权限。

基本语法如下：

    CREATE [RECURSIVE]VIEW view_name
    [COLUMN_NAME [,COLUMN_NAME]]
    [OF UDT_NAME [UNDER TABLE_NAME]
    [REF IS COLUMN_NAME SYSTEM GENERATED | USER GENERATED | DERIVED]
    COLUMN_NAME WITH OPTIONS SCOPE TABLE_NAME]
    AS
    {SELECT STATEMENT}
    [WITH [CASCADE | LOCAL] CHECK OPTION]

*AMSI SQL不包含ALTER VIEW语句，但是大多数实现都支持ALTER VIEW语句。*

##### 20.2.1 从一个表创建视图

语法如下：

    CREATE VIEW VIEW_NAME AS
    SELECT * | COLUMN1 [, COLUMN2]
    FROM TABLE_NAME
    [WHERE CONDITIONS]
    [WITH CHECK OPTION]
    [GROUP BY]

示例：

    -- 基于单个表的全部内容创建视图
    CREATE VIEW products_view AS
    SELECT * FROM products_tbl;
    -- 基于单个表中的指定字段创建视图
    CREATE VIEW emp_view AS
    SELECT LAST_NAME, FIRST_NAME
    FROM employee_tbl;

示例：下面的SQL语句创建了表employee_tbl的视图

    CREATE VIEW names_city AS
    SELECT CONCAT(FIRST_NAME, '·', LAST_NAME) NAMES, CITY
    FROM employee_tbl;

然后我们可以从视图NAMES里选择全部数据：

    SELECT * FROM names_city;

返回数据：

NAMES |  CITY
-----|-----
JOHN·WILL |  NEW YORK
TIM·TOMPSON | D.C.
FRANK·RYAN | GREENWOOD
ZOE·ARTHUR | GREENWOOD
TINA·GLASS | WHITELAND
JACOB·GLASS| INDIANAPOLIS
MARIAH·WALLACE | WHITELAND
LINDA·PLEW | INDIANAPOLIS

示例：创建基于多个表包含摘要的视图

    CREATE VIEW city_pay AS 
    SELECT E.CITY, AVG(P.PAY_RATE) AVG_PAY
    FROM employee_tbl E, employee_pay_tbl P
    WHERE E.EMP_ID = P.EMP_ID
    GROUP BY E.CITY;

下面对这个视图进行查询：

    SELECT * FROM city_pay;

返回数据：

CITY  |  AVG_PAY
----- | -----
D.C.  |  11.400000
GREENWOOD |  12.650000
INDIANAPOLIS |   17.800000
NEW YORK  |  10.000000
WHITELAND |  15.300000

上述创建视图的语句同样可以通过JOIN实现：

    CREATE VIEW city_pay AS 
    SELECT E.CITY, AVG(P.PAY_RATE) AVG_PAY
    FROM employee_tbl E JOIN employee_pay_tbl P
    ON E.EMP_ID = P.EMP_ID
    GROUP BY E.CITY;

##### 20.2.3 从视图创建视图

可以使用下面的语法从一个视图中创建俩一个视图：

    CREATE VIEW2 AS SELECT * FROM VIEW1;

视图创建视图可以具有多个层次，允许的层次取决于具体的实现。**基于视图创建视图的唯一问题在于它们的可管理性。**

举例来说，基于VIEW1创建了VIEW2，又基于VIEW2创建了VIEW3。如果VIEW1被删除了，那么VIEW2和VIEW3也就不可用了，因为支持这些视图的底层数据不存在了。因此我们需要始终很好地理解数据库里的视图，以及它们依赖于什么数据库对象。

#### 20.3 WITH CHECK OPTION

这是CREATE VIEW语句里的一个选项，其nudity是确保全部的UPDATE和INSERT语句满足视图定义的条件。如果它们不满足条件，UPDATE或INSERT语句就会返回错误。WITH CHECK OPTION实际上通过查看视图定义是否被破坏来确保引用完整性。

示例：创建视图emp_payrate_tmp

    CREATE VIEW emp_payrate_tmp AS
    SELECT PAY_RATE, FIRST_NA, LAST_NAM
    FROM emp_pay_tmp
    WHERE PAY_RATE IS NOT NULL
    WITH CHECK OPTION;

在这个视图中，WITH CHECK OPTION会确保视图定义所依赖的数据里不允许在PAY_RATE字段插入NULL值。

示例：像视图插入数据

    INSERT INTO emp_payrate_tmp VALUES(NULL, 'RICK', 'RITTER');

此时会发生报错：

    Error Code: 1369
    CHECK OPTION failed '_studysql.emp_payrate_tmp'

#### 20.4 从视图创建表

我们可以从视图创建表，就像从一个表创建另一个表（或从视图创建另一个视图）一样。

语法如下：

    CREATE TABLE TABLE_NAME AS
    SELECT {* | COLUMN1 [, COLUMN2]}
    FROM VIEW_NAME
    [WHERE CONDITIONS]
    [ORDER BY]

示例：

    CREATE TABLE city_pay_tmp
    SELECT CITY, AVG_PAY 
    FROM city_pay;

#### 20.5 视图与GROUP BY子句

CREATE VIEW语句不能包含ORDER BY子句，但是可以使用GROUP BY子句。

示例：

    CREATE VIEW city_pay AS 
    SELECT E.CITY, AVG(P.PAY_RATE) AVG_PAY
    FROM employee_tbl E, employee_pay_tbl P
    WHERE E.EMP_ID = P.EMP_ID
    GROUP BY E.CITY;

#### 20.6 通过视图更新数据

在一定条件下，视图的底层数据可以进行更新：

- 视图不包含结合
- 视图不包含GROUP BY子句
- 视图不包含UNION语句
- 视图不包含对伪字段ROWNUM的任何引用
- 视图不包含任何组函数
- 不能使用DISTINCT命令
- WHERE子句包含的嵌套的表表达式不能与FROM子句引用一个表
- 视图可以执行INSERT、UPDATE和DELETE语句

#### 20.7 删除视图

DROP VIEW命令用于从数据库里删除视图，它有两个选项RESTRICT和CASCADE。如果使用了RESTRICT选项进行删除操作，而其他视图在约束中有所引用，删除操作就会出错。如果使用了CASCADE选项，而且其他视图或约束被引用了，DROP VIEW也会成功，并且底层的视图或约束也会被删除。

语法如下：

    DROP VIEW VIEW_NAMES;

#### 20.8 嵌套视图对性能的影响

在查询中使用视图，与使用表有着相同的性能特性。因此，用户必须意识到，在视图中隐藏复杂的逻辑会导致系统需要查询底层表来分析并组合数据。在进行性能调整时，视图需要和其他SQL语句一样被调整。如果构成视图的查询没有经过事先设计，那么视图本身就会对性能产生影响。

#### 20.9 什么是异名

*MySQL不支持异名。*

异名就是表或视图的另一个名称。我们创建别名通常是为了在访问其他用户的表或视图时不必使用完整限制名。异名可以创建为PUBLIC或PRIVATE，PUBLIC的异名可以被数据库里的其他用户使用，而PRIVATE异名只能被所有者和拥有权限的用户使用。通常来说只有DBA可以创建PUBLIC异名。

##### 20.9.1 创建异名

语法如下：

    CREATE [PUBLIC | PRIVATE] SYNONYM SYNONYM_NAME FOR TABLE|VIEW

示例：

    CREATE SYNONYM cust FOR customer_tbl;

然后就可以使用异名来访问：

    SELECT CUST_NAME FROM cust; 

#### 20.9.2 删除异名

    DROP [PUBLIC|PRIVATE] SYNONYM SYNONYM_NAME;

### 第21章 使用系统目录

本章介绍系统目录，在某些关系型数据库的实现里，这也被称为数据目录。本章将介绍系统目录的作用与内容，以及如何对它进行查询来获得数据库信息。每种主流实现都具有某种形式的系统目录，保存了关于数据库本身的信息。

#### 21.1 什么是系统目录

系统目录是一些表和视图的集合，它们包含了关于数据库的信息。每个数据库都由自己的系统目录，其中定义了数据库的结构，还有数据库所包含数据的信息。

系统目录实际是数据库的一部分。数据库里包含的是对象，系统目录基本上就是一组对象，包含了定义数据库里其他对象的信息，数据库本身的结构以及其他各种重要的信息。
 
#### 21.2 如何创建系统目录

系统目录是在数据库创建时自动创建的，或是有DBA在数据库创建之后立即创建的。

系统目录的表和视图是由系统所拥有的，不属于任何规划。

- Oracle：系统目录的所有者是一个名为SYS的用户
- SQL Server：SQL服务程序目录位于master数据库里
- MySQL：系统目录唯一mysql数据库

#### 21.3 系统目录包含的内容

系统目录包含：

- 用户账户和默认设置
- 权限和其他安全信息
- 性能统计
- 对象大小估计
- 对象变化
- 表结构和存储
- 索引结构和存储
- 数据库其他对象的信息，比如视图、异名、触发器和存储过程
- 表约束和引用完整性信息
- 用户会话
- 审计信息
- 内部数据库位置
- 数据库文件的位置

系统目录由数据库服务程序维护。

##### 21.3.1 用户数据

关于个人用户的全部信息都保存在系统目录：

- 用户具有的系统和对象权限
- 用户拥有的对象
- 用户不用有但能访问的对象

##### 21.3.2 安全信息

系统目录也保存安全信息，比如用户标识、加密的密码、各种权限和权限组。有些实现里还包含审计表，可以跟踪数据库发生的事件，包括由谁引发、何时等信息。在很多实现中，利用系统目录还可以密切监视数据库用户会话。

##### 21.3.3 数据库设计信息

系统目录包含关于数据库的信息，包括数据库的创建日期、名称、对象大小估计、数据文件大小和位置、引用完整性信息、索引、每个表的字段信息和属性。

##### 21.3.4 性能统计

性能统计一般也在系统目录里，包括关于SQL语句性能的信息，比如优化器执行SQL语句的性能和方法。其他性能信息还有内存分配和使用、数据库里剩余空间、控制表格和索引碎片的信息。利用这些信息可以调整数据库，重新安排SQL查询，重新设计访问数据的方法，从而得到更好地整体性能和更快的SQL查询响应。

#### 21.4 不同实现里的系统目录表格

*Oracle、SQL Server略。*

MySQL中系统目录表格部分内容：

表格名称 | 内容
-----|-----
COLUMNS_PRIV | 字段权限
DB | 数据库权限
FUNC | 自定义函数的管理
TABLES_PRIV | 表权限
USER | 用户权限

#### 21.5 查询系统目录

我们可以像对待数据库里其他表格和视图一样使用SQL查询系统目录里的表格和视图。

用户通常只能查询与用户相关的表，不能访问系统表，后者通常只能由被授权的用户访问。

示例：查询_studysql规划下的全部表它运行于information_schema上

    SELECT table_name FROM TABLES WHERE table_schema = '_studysql';

返回数据：

<table>
    <tr>
        <td bgcolor=silver class='medium'>table_name</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>city_pay</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>city_pay_tmp</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>course</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>emp</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>emp_pay_tmp</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>emp_payrate_tmp</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>employee_pay_tbl</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>employee_tbl</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>names_city</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>orders_tbl</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>products_tbl</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>products_tmp</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>rich_employee</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>rollup</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>sc_middle</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>student</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>t1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>t2</td>
    </tr>
</table>

## 第八部分 在实际工作中应用SQL知识

### 第22章 高级SQL主题

本章介绍一些高级SQL主题，内容包括游标、存储过程、触发器、动态SQL、直接SQL与嵌入SQL、SQL生成SQL。

某些主题并不属于ANSI SQL，所以其实际语句和规则要取决于具体实现。+

*以下以MySQL为例。*

#### 22.1 游标

通常，数据库操作被认为是已数据集为基础的操作。这意味着，大部分ANSI SQL命令是作用于一组数据的。但是，关闭则被用于通过以记录为单位的操作，来获得数据库中数据的子集。因此，程序可以依次对游标里的每一行进行求值。游标一般用于过程化程序里嵌入的SQL语句。有些游标是由数据库服务程序自动隐含创建的，有些是由SQL程序员定义的。每个SQL实现里对游标用法的定义是不同的。

MySQL对游标的声明：

    DELCARE CURSOR_NAME CURSOR FOR SELECT_STATEMENT

表employee_tbl全部子集的游标：

    DECLARE emp_cursor CURSOR FOR SELECT * FROM employee_tbl;

##### 22.1.1 打开游标

要使用游标，必须首先打开游标。当游标被打开时，指定的游标的SELECT语句被执行，查询的结果被保存在内存的特定区域中。

语法：

    OPEN CURSOR_NAME

##### 22.1.2 从游标获取数据

在游标被打开后，我们可以使用FETCH语句获取游标的内容（查询的结果）。

语法：

    FETCH CURSOR_NAME INTO VARIABLE_NAME [, VARIABLE_NAME] ...

在从游标获得数据时，需要注意可能会到达游标末尾。不同的实现使用不同的方法来解决这个问题，从而避免用户在游标关闭的时候产生错误。

*MySQL中游标需要在存储过程中定义。*

示例：

    DELIMITER $; -- 设置结束标志

    CREATE PROCEDURE getemployee() -- 创建存储过程

    BEGIN
        DECLARE emp_id CHAR(6); -- 声明变量emp_id
        DECLARE city VARCHAR(20); -- 声明变量city
        DECLARE NAME VARCHAR(20); -- 声明变量NAME
        
        DECLARE done INT DEFAULT FALSE; -- 声明遍历数据结束标志
        
        -- 声明游标
        DECLARE cur CURSOR FOR SELECT EMP_ID, CITY, CONCAT(FIRST_NAME, '·', LAST_NAME) FROM employee_tbl;
        
        -- 将结束标志绑定到游标
        DECLARE CONTINUE HANDLER FOR NOT FOUND SET DONE = TRUE;
        
        -- 开启游标
        OPEN cur;
        
        -- 开始遍历
        read_loop: LOOP
            
            -- 从游标中获取值
            FETCH NEXT FROM cur INTO emp_id, city, NAME;
            
            -- 如果游标到达末尾
            IF done THEN
            
            -- 结束遍历
            LEAVE read_loop;
        
            END IF;
            
        -- 结束遍历
        END LOOP;
        
        -- 关闭游标
        CLOSE cur;

    END $;


##### 22.1.3 关闭游标

游标可以打开，当然可以关闭。在游标关闭之后，程序就不能再使用它了。

语法：

    CLOSE CURSOR_NAME

#### 22.2 存储过程和函数

存储过程是一组相关联的SQL语句，通常被称为函数和子程序，能够让程序员更轻松和更灵活地编程。这是因为存储过程与一些列单个SQL语句相比更容易执行。存储过程可以嵌套在另一个存储过程中。也就是说存储过程可以调用其他存储过程。

利用存储过程可以实现过程化编程。基本的SQL DDL（数据定义语言）、DDL（数据管理语言）和DQL（数据查询语言）语句只是告诉数据库需要做什么，而不是如何去做。而通过对存储过程进行编程，我们就可以告诉数据库引擎如何处理数据。

存储过程是保存在数据库里的一组SQL语句或函数，它们被编译，随时可以被数据库用户使用。存储函数与存储过程是一样的，但函数可以返回一个值。

函数由过程调用。当函数被过程调用时也可以传递参数，函数会进行所需要的计算，并把一个值返回给调用它的过程。

当存储过程被创建后，组成它的各种子程序和函数都保存在数据库里。这些存储过程经过了预编译，可以随时供用户调用。

MySQL中创建存储过程的语法：

    CREATE [ OR REPLACE] PROCEDURE PROCEDURE_NAME
    [([{IN | OUT | IN OUT}] ARGUMENT TYPE,
    [{IN | OUT | IN OUT}] ARGUMENT TYPE)]
    PROCEDURE_BODY

示例：

    DELIMITER $;
    CREATE PROCEDURE new_product
    (IN PROD_ID CHAR(10), IN PROD_DESC VARCHAR(25), IN COST DECIMAL(6,2))
    BEGIN 
        INSERT INTO products_tbl VALUES(PROD_ID, PROD_DESC, COST);
        SELECT * FROM products_tbl;
    END $;

MySQL调用执行过程：

    CALL PROCEDURE_NAME(PARAMETERS)

示例：调用储存过程new_product

    CALL new_product('230722', 'VAMPIRE SUIT', 35.99);

结果对表products_tbl插入新行，并查询表的全部数据。

与单个SQL语句相比，存储过程具有一些明显的优点：

- 存储过程的语句已经保存在数据库里了
- 存储过程的语句已经被解析过，以可执行格式存在
- 存储过程支持模块化编程
- 存储过程可以调用其它存储过程和函数
- 存储过程可以被其他类型的程序调用
- 存储过程通常具有更好的响应时间
- 存储过程提高了整体易用性

#### 22.3 触发器

触发器是数据库里编译了的SQL过程，基于数据库里发生的其他型为来执行奥做。它是存储过程的一种，会在特定DML型为作用于表时被执行。它可以在INSERT、UPDATE和DELETE语句之前或之后执行，可以在这些语句之前检查数据完整性，可以回滚事务，可以修改一个表里的数据，可以从另一个数据库里读取数据。

在大多数情况下，触发器是很不错的函数，但它们会导致更多的I.O开销。如果使用存储过程或程序能够在较少开销下完成同样的工作，就应该尽量避免使用触发器。

##### 22.3.1 CREATE TRIGGER语句

MySQL里使用触发器的语法是：

    CREATE
        [DEFINER = { user | CURRENT_USER }]
        TRIGGER trigger_name
        trigger_time trigger_event
        ON tbl_name FOR EACH ROW
        trigger_body

示例：这个触发器将在向表products_tbl插入数据时执行“具体操作”。

    DELIMITER $;
    CREATE TRIGGER tri_insert_prod 
    AFTER INSERT ON products_tbl FOR EACH ROW 
    BEGIN
        -- 具体操作
    END $;

*这个具体操作不能是SELECT语句，否则发生报错Error Code: 1415 Not allowed to return a result set from a trigger*。

##### 22.3.2 DROP TRIGGER语句

这个语句可以删除触发器：

    DROP TRIGGER TRIGGER_NAME;

示例：

    DROP TRIGGER tri_insert_prod;

#### 22.4 动态SQL

动态SQL允许程序员或终端用户在运行时创建SQL语句的具体代码，并且把语句传递给数据库。数据库然后就把数据返回到绑定的程序变量里。

静态SQL是事先编写好的，不准备进行改变的。虽然静态SQL语句可以保存到文件里以备以后使用，也可以作为存储过程保存在数据库里，但其灵活性还是不能与动态SQL相比。

使用调用及接口可以创建动态SQL。

#### 22.5 调用级接口

调用级接口（CLI）用于把SQL代码嵌入到主机程序。

常见的支持CLI的编程语言：

- ANSI C
- C#
- VB.NET
- JAVA
- PASCAL
- FORTRAN

#### 22.6 使用SQL生成SQL

使用SQL生成SQL是节省SQL语句编写时间的一个好方法。

假设数据库有100个用户，我们创建一个新角色ENABLE，并要授予给这100个用户。以Oracle为例，我们不用手工创建100个GRANT语句，下面的SQL语句会生成所需的每一条语句：

    SELECT 'GRANT ENABLE TO ' || USERNAME || ';' FROM SYS.DBA_USERS;

这个SQL语句的结果是这样的：

    GRANT ENABLE TO RRPLEW;
    GRANT ENABLE TO RKSTEP;
    ...




