[TOC]

# Learning SQL - - - SQL学习指南

*使用MySQL 5.7.19实现，搭配SQLyog图形化工具。*

## 第一章 数据库简介

### 1.1 数据库简介 

#### 1.1.1 非关系数据库

略。

#### 1.1.2 关系模型

略

#### 1.1.3 一些术语

术语 | 定义
-----|-----
实体|数据库用户所关注的对象
列|存储在表中的独立数据片段
行|所有列的一个集合，完整地描述了一个实体或实体上的某个行为，也称为记录
表|行的集合，既可以保存在内存中（未持久化），也可以保存在存储设备中（已持久化）
结果集|未持久化表的另一个名字，一般为SQL查询结果
主键|用于唯一标识表中每个行的一个或多个列
外键|一个或多个用于识别其他表中某一行的列

### 1.2 什么是SQL

#### 1.2.1 SQL语句分类

在本书中，将分别讨论SQL语言的几个独立模块，即SQL方案（schema）语句，用于定义存储于数据库中的数据结构；SQL数据语句，用于操作SQL方案语句所定义的数据结构；以及SQL事务语句，用于开始、结束或回滚事务。

示例：

    -- 创建表
    CREATE TABLE corporation
    (
        corp_id SMALLINT,
        NAME VARCHAR(30),
        CONSTRAINT pk_corporation PRIMARY KEY(corp_id)
    );

    -- 插入数据
    INSERT INTO corporation (corp_id, NAME) VALUES (27, 'Acme Paper Corporation');

    -- 查询
    SELECT NAME FROM corporation WHERE corp_id = 27;

通过SQL方案语句所创建的所有数据库元素都被存储在一个特殊的表集合，即数据字典中。这些“关于数据库的数据”一般被称为“元数据”。与用户所创建的表一样，数据字典表也可以通过select语句查询，从而允许在运行时刻查看数据库中的当前数据结构。

#### 1.2.2 SQL：非过程化语句

使用SQL意味着必须放弃对过程的控制，因为SQL语句之定义必要的输入和输出，而执行语句的方式则交由数据库引擎的一个组建，即优化器处理。优化器的工作包括查看SQL语句并考虑该表的配置信息以及有无索引等，以确定最具效率的执行路径（不总是）。大多数数据库引擎允许通过指定优化器选项来影响优化器的决策，比如建议使用特定的索引等。而大多数SQL用户并不需要考虑这个复杂的层面，而是将之交给数据库管理员或性能调优专家来处理。

因此，单独使用SQL并不能开发完整的应用，除了编写简单的脚本来处理某些数据，一般还需要将SQL与编程语言相集成。例如，Oracle的PL/SQL语言，MySQL的存储过程语言，以及SQL Server的Transact-SQL语言。在这些语言中，SQL数据语言是其语法的一部分，以准确无误地将数据库查询与过程化命令集成在一起。如果使用非数据库指定的语言，则需要使用工具集/API以在代码中执行SQL语句。

例如：

语言|工具集
-----|-----
Java|JDBC（Java Database Connectivity）
C++|Rogue Wave SourcePro DB（Third-party tool to connect to Oracle, SQL Server, MySQL, Informix, DB2, Sybase and PostgreSQL database）
C/C++|Pro*C(Oracle), MySQL C API(open source), and DB2 Call Level Interface(IBM)
C#| ADO.NET(Microsoft)
Perl|Perl DBI
Python|Python DB
Visual Basic|ADO.NET(Microsoft)

*过程化语言对所期望的结果和产生这些结果的执行机制或过程都进行了定义。非过程化语言同样定义了期望结果，但将产生结果的过程留给外部代理来定义。*

#### 1.2.3 SQL示例

一个相对复杂的查询语句：

    SELECT t.txn_id, txtxn_type_cd, t.txn_date, t.amount
    FROM individual i
        INNER JOIN account a ON i.cust_id = a.cust_id
        INNER JOIN product p ON p.product_cd = a.product_cd
        INNER jpin TRANSACTION t ON t.account_id = a.account_id
    WHERE i.fname = 'Georage' AND i.lname = 'Blake' AND p.name = 'checking account';

上述查询包含了3个不同的子句，包括select、from和where。几乎所有的查询都至少会包含这3个子句，当然还有其他几个子句可用于更特定的查询目标。

在任何时刻执行SQL数据语句都会收到一个来自数据引擎的反馈，以显示该语句所影响的行数。如果使用交互式工具，例如mysql命令行工具，那么可以接收到下面几种操作所影响行数的返回：

- select语句查询的行数
- insert语句创建的行数
- update语句修改的行数
- delete语句删除的行数

可以使用过程化语言，并结合上文提到的工具集来调用SQL语句。工具集通常包含了能够获取SQL数据语句执行信息的调用。一般来说，好的做法应该是检查这个信息以确信语句执行没有超出预料。

## 第二章 创建和使用数据库 

>
本章内容包括如何创建数据库、如何创建表，并且相关的数据将会作为全书的用例（*LearningSQLExample.sql*）。

### 2.1 创建MySQL数据库

步骤：后续内容不使用root用户

1. 安装MySQL数据库服务器并配置MySQL环境变量
2. 打开CMD，先使用root用户登陆系统：mysql -u root -p，然后输入密码
3. 创建示例用数据库：create database bank;
4. 创建用户lrngsql，并赋予bank数据库的权限：grant all privileges on bank.* to 'lrngsql'@'localhost' identified by 'mysql';
5. 退出CMD
6. 使用lrngsql用户登录MySQL：mysql -u lrngsql -p，然后输入密码
7. 使用bank数据库：use bank;
8. 使用LearningSQLExample.sql生成相关数据

### 2.2 使用mysql命令行工具

在调用mysql命令行工具时可以同时指定用户名和要使用的数据库，如下所示：

     mysql -u -lrngsql -p bank

这样可以省去每次启动工具时都要输入use bank;命令的麻烦。输入完成后会立即出现mysql>标记，在该标记后面可以运行SQL语句并查看输出结果。

示例：查询当前时间

    select now();

返回结果：

<table>
    <tr>
        <td>now</td>
    </tr>
    <tr>
        <td>2018-03-11 22:32:56</td>
    </tr>
</table>

now()是内建的MySQL函数，它返回当前日期与时间。

使用完mysql命令行，可以简单地输入quit或exit，以返回Windows shell。

**缺失的子句：**
某些数据库服务器规定查询语句中必须包含from子句，并在其中至少指明一个表名，例如Orcale。这时，如果是仅仅需要调用一个函数，Oracle为此提供了一个特殊的表dual，该表质保函一个名为dummy的列，并且只会有一个数据行。为了与Oracle数据库保持兼容，MySQL也提供了dual表，因此上述对当前日期的查询可以使用下述语句：

    SELECT NOW() FROM DUAL;

### 2.3 MySQL的数据类型

一般来说，所有流行的数据库都可以存储同样的数据类型，比如字符串、日期和数字等，但对于一些特殊的数据类型，比如XML文档或大的文本及二进制文档，各种数据库之间存在着较为明显的差异。

#### 2.3.1 字符型数据

字符型数据可以使用定长或变长的字符串来实现，其不同点在于固定长度的字符串使用空格向右填充，以保证占用同样的字节数；变长字符串不需要向右填充，并且所有字节数可变。例如，需要存储最大长度不超过20个字符的字符串，可以使用下面的定义方式：

    char(20) /* fixed-length */
    varchar(20) /* variable-length */

*定义varchar时必须指明最大字符数。*

char列可以设置最大长度为255个字节，而varchar列最多可以存储65535个字节。如果需要存储更长的字符串，例如XML文档，则需要使用文本类型（mediumtext和longtext）。一般情况下，使用char类型来存储同样长度的字符串，比如州名缩写，以及使用varchar类型存储变长字符串。在所有主流数据库中，char和varchar的使用方式都是类似的。

*Oracle数据库对varchar的使用是特例，Oracle使用varchar2类型表示变长字符串列。*

**字符集**

对于拉丁系语言，比如英语，包含了一系列字母，其中每个字母只需要1个字节来存储。其他一些语言（中文等）则包含了大量字符。可以使用show命令来查看服务器所支持的字符集。

示例：

    SHOW CHARACTER SET;

返回结果：

Charset|Description|Default collation|Maxlen
-----|-----|-----|-----
big5|Big5 Traditional Chinese|big5_chinese_ci|2
dec8|DEC West European|dec8_swedish_ci|1
cp850|DOS West European|cp850_general_ci|1
hp8|HP West European|hp8_english_ci|1
koi8r|KOI8-R Relcom Russian|koi8r_general_ci|1
latin1|cp1252 West European|latin1_swedish_ci|1
latin2|ISO 8859-2 Central European|latin2_general_ci|1
swe7|7bit Swedish|swe7_swedish_ci|1
ascii|US ASCII|ascii_general_ci|1
ujis|EUC-JP Japanese|ujis_japanese_ci|3
sjis|Shift-JIS Japanese|sjis_japanese_ci|2
hebrew|ISO 8859-8 Hebrew|hebrew_general_ci|1
tis620|TIS620 Thai|tis620_thai_ci|1
euckr|EUC-KR Korean|euckr_korean_ci|2
koi8u|KOI8-U Ukrainian|koi8u_general_ci|1
gb2312|GB2312 Simplified Chinese|gb2312_chinese_ci|2
greek|ISO 8859-7 Greek|greek_general_ci|1
cp1250|Windows Central European|cp1250_general_ci|1
gbk|GBK Simplified Chinese|gbk_chinese_ci|2
latin5|ISO 8859-9 Turkish|latin5_turkish_ci|1
armscii8|ARMSCII-8 Armenian|armscii8_general_ci|1
utf8|UTF-8 Unicode|utf8_general_ci|3
ucs2|UCS-2 Unicode|ucs2_general_ci|2
cp866|DOS Russian|cp866_general_ci|1
keybcs2|DOS Kamenicky Czech-Slovak|keybcs2_general_ci|1
macce|Mac Central European|macce_general_ci|1
macroman|Mac West European|macroman_general_ci|1
cp852|DOS Central European|cp852_general_ci|1
latin7|ISO 8859-13 Baltic|latin7_general_ci|1
utf8mb4|UTF-8 Unicode|utf8mb4_general_ci|4
cp1251|Windows Cyrillic|cp1251_general_ci|1
utf16|UTF-16 Unicode|utf16_general_ci|4
utf16le|UTF-16LE Unicode|utf16le_general_ci|4
cp1256|Windows Arabic|cp1256_general_ci|1
cp1257|Windows Baltic|cp1257_general_ci|1
utf32|UTF-32 Unicode|utf32_general_ci|4
binary|Binary pseudo charset|binary|1
geostd8|GEOSTD8 Georgian|geostd8_general_ci|1
cp932|SJIS for Windows Japanese|cp932_japanese_ci|2
eucjpms|UJIS for Windows Japanese|eucjpms_japanese_ci|3
gb18030|China National Standard GB18030|gb18030_chinese_ci|4

其中如果第四列maxlen大于1，那么该字符集为多字符集。

我们可以为数据库设置默认字符集，还可以为数据库中的每个表设置字符集，也可以为字符列选择不同的字符集，甚至可以在同一个数据表内存储不同的字符集数据。为数据列指定非默认字符集，只需要在类型定义后加上系统支持的字符集名称即可，例如：

    VARCHAR(20) CHARACTER SET utf8  

在创建表时指定字符集：

    CREATE TABLE table_name
    (
        ...
    ) ... DEFAULT CHARSET = utf8

创建数据库时指定字符集/改变数据库的字符集：

    ALTER DATABASE database_name DEFAULT CHARACTER SET gbk; 

**文本数据**

如果需要存储的数据超过64KB（VARCHAR列所能容纳的上限），就需要使用文本类型。

下表列出了可用的文本类型及它们的最大长度：

文本类型|Maximum number of bytes
----- | -----
tinytext | 255
text | 65535
mediumtext | 16777215
longtext | 4294967295

当使用文本类型时，应注意下列事项：

- 如果被装载到文本列中的数据超出了该类型的最大长度，数据将会被截断。
- 在向文本列装在数据时，不会消除数据尾部的空格。
- 当使用文本列排序或分组时，只会使用前1024个字节，当然在需要时可以放宽这个限制。
- MySQL的VARCHAR在latin1字符集下最多可以定义为VARCHAR(65535)（在utf8下最多可以定义为VARCHAR(21845)），可以保存65535字节的数据，这样一般就不需要使用tinytext或text类型了。

#### 2.3.2 数值型数据

尽管使用独立的数值数据类型“numeric”似乎更加合理，但实际上存在着好几种不同的数值数据类型，它们反应了数字应用的不同方式。

为了处理这些类型的数据，MySQL提供几种不同的数值数据类型，最常用的是用于存储整数的类型，在这些类型前面还可以加上unsigned关键字，以向服务器指明该列存储的数据大于等于0。

MySQL的整数类型：

类型|带符号的范围|无符号范围
-----|-----|-----
tinyint | -128 ~ 127 | 0 ~ 255
smallint | -32768 ~ 32767 | 0 ~ 65535
mediumint | -8388608 ~ 8388607 | 0 ~ 16777215
int | -2147483648 ~ 2147483647 | 0 ~ 4294967295
bigint | -9233372036854775808 ~ 9223372036854775807 | 0 ~ 18446744073709551615

当使用这些整数类型之一创建列时，MySQL将为存储数据分配合适的大小空间，从1个字节（tinyint）到8个字节（bigint）不等。因此在选择类型时，只需要确保能够容纳预期的最大数字即可，这样可以避免浪费不必要的存储空间。

对于浮点数类型，我们可以使用下面的类型：

类型 | 数值范围
-----|-----
float(p,s) | -3.402823466E+38 ~ -1.175494351E-38，0，1.175494351E-38 ~ 3.402 823 466351E+38
double(p,s) |-1.7976931348623157E+308 ~ -2.2250738585072014E-308，0，2.225 073 8585072014E-308 ~ 1.7976931348623157E+308

当使用浮点类型时，可以指定其精度（数字总位数）和有效位（小数点右边允许的数字位数），当然这不是必须的。这两个值由参数p和s指定。需要注意的是，如果数字位数超过了列所定义的精度或有效位，那么该列中存储的数据将会被四舍五入。例如一个定义为float(4,2)的列将会存储4位数字，其中两位在小数点左边，两位在小数点右边。因此，如果向该列添加数据27.44和8.19是允许的，但17.8675将会被四舍五入为17.87，而178.375则会产生一个错误。

和整形类型一样，浮点列也可以被定义为unsigned，但这里只是禁止列中存放负数，并没有改变列所存储数据的范围。

#### 2.3.3 时间数据

除了字符串和数字，处理信息还会经常用到日期或时间。

对于时间型数据，MySQL支持的时间数据类型有：

类型 | 默认格式 | 允许的值
-----|-----|-----
date | YYYY-MM-DD | 1000-01-01 ~ 9999-12-31
datetime | YYYY-MM-DD HH:MI:SS | 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59
timestamp | YYYY-MM-DD HH:MI:SS | 1970-01-01 00:00:00 ~ 2037-12-31 23:59:59
year |YYYY | 1901 ~ 2155
time |HHH:MI:SS | -838:59:59 ~ 838:59:59

#### 2.3.4 二进制类型

对于二进制类型，MySQL使用blob类型存储。包括tinyblob、blob、mediumblob和longblob。

类型 | 大小
-----|-----
tinyblob|0-255字节
blob|0-65 535字节
mediumblob|0-16 777 215字节
longblob|0-4 294 967 295字节

### 2.4 表的创建

下面从定义一张存放个人信息的表开始。

#### 2.4.1 第1步：设计

在开始设计一张表之前，最好对确定需要包含哪些信息进行谨慎的思考。在这里我们选择以下信息用于描述个人信息：

- 姓名
- 性别
- 出生日期
- 地址
- 最喜爱的食物

接着，为每一列指定数据类型。

person表 - - - 初步结果：

列 | 类型 | 允许值
-----|-----|-----
name | varchar(40) | 
gender | char(1)| M, F
birth_date | date | 
address | varchar(100) |
favorite_foods | varchar(200) | 

#### 2.4.2 第2步：精化

下面要对person表进行简单的规范化，即在数据库设计时确保没有重复（外键除外）或复合列。person表当前存在的问题：

- name列实际上是包含了姓氏和名字的复合对象。
- 可能存在多个人具有相同名字、性别、生日等，而person表中并没有列来保证唯一性。
- address列也是包含了解到、州/省名、县市名和邮政编码的复合对象。
- favorite_foods列可以是包含0、1或更多条目的列表，因此最好为此数据创建一个独立的表，其中包含一个指向person表的外键，以便为每一种食物指明所归属的人员。
- 
person表 - - - 第2步结果：

列 | 类型 | 允许值
-----|-----|-----
person_id | smallint(unsigned) | 
first_name | varchar(20) | 
last_name | varchar(20) | 
gender | char(1) | M,F
birth_date | date | 
street | varchar(30) | 
city | varchar(20) |
state | varchar(20) | 
country | varchar(20) | 
postal_code | varchar(20) | 

这里person_id作为主键，来保证唯一性，下面建立favorite_food表，其中包含一个指向person表的外键：

favorite_food表：

列 | 类型
-----|-----
person_id | smallint(unsigned)
food | varchar(20)

这里person_id和food列构成了favorite_food表的主键，并且person_id列也是person表的外键。

#### 2.4.3 第3步：构建SQL方案语句

完成表的设计后，接下来就是生成在数据库中创建表的SQL语句。

创建person表的语句如下：

    CREATE TABLE person
    (
        person_id SMALLINT UNSIGNED,
        fname VARCHAR(20),
        lname VARCHAR(20),
        gender CHAR(1),
        birth_date DATE,
        street VARCHAR(30),
        city VARCHAR(20),
        country VARCHAR(20),
        postal_code VARCHAR(20),
        CONSTRAINT pk_person PRIMARY KEY (person_id)
    );

在定义表时，需要向数据库指明哪个列或哪些列作为表的主键，通过为表建立一个约束可以做到这一点。可以在定义表时增加多种类型的约束，上述语句中的约束为主键约束，它被创建在person_id列上并被命名为pk_person。

对于person表的gender字段来说，它只接收特定的值（M, F），这时可以给它增加一个检查约束。MySQL允许在定义列时关联一个检查约束，以限制该列只存放被允许的值。

示例：

    gender CHAR(1) CHECK (gender IN ('M', 'F')),

**尽管大多数数据库服务器中检查约束可以如期望般那样工作，但是对于MySQL而言，虽然允许定义检查约束，但并不强制使用它。**实际上MySQL提供了另一种名为enum的字符数据类型，它可以将检查约束与数据类型定义融合在一起。

示例：

    gender ENUM('M', 'F'),

下面是使用enum作为gender字段数据类型的person表：

    CREATE TABLE person
    (
        person_id SMALLINT UNSIGNED,
        fname VARCHAR(20),
        lname VARCHAR(20),
        gender ENUM('M', 'F'),
        birth_date DATE,
        street VARCHAR(30),
        city VARCHAR(20),
        state VARCHAR(20),
        country VARCHAR(20),
        postal_code VARCHAR(20),
        CONSTRAINT pk_person PRIMARY KEY (person_id)
    );

在创建表之后，如果想查看表的定义可以使用describe命令（或其简写desc）：

    DESC person;

返回结果：

Field|Type|Null|Key|Default|Extra
-----|-----|-----|-----|-----|-----
person_id|smallint(5) unsigned|NO|PRI|NULL|
fname|varchar(20)|YES| |NULL|
lname|varchar(20)|YES| |NULL|
gender|enum('M','F')|YES| |NULL|
birth_date|date|YES| |NULL|
street|varchar(30)|YES| |NULL|
city|varchar(20)|YES| |NULL|
state|varchar(20)|YES| |NULL|
country|varchar(20)|YES| |NULL|
postal_code|varchar(20)|YES| |NULL|

返回数据中，第一列为字段名称；第二列为数据类型；第三列显示该列是否允许在插入数据时被省略；第四列显示该列是否作为键值（主键或外键）；第五列显示如果在插入数据时忽略该列，是否提供默认值；第六列显示该列的附加说明信息。

**什么是NULL：**
在某些情况下，在向表中插入数据时，无法为其中某一列提供具体的值，此时该列被设置为NULL，以指明该值的缺失。NULL被用于各种不能赋值的情况。例如：

- 业务上不可行
- 不知道应赋何值
- 集合为空

在设计表时，可以指定哪些列允许为NULL（默认做法），哪些列不能为NULL（使用NOT NULL指定）。

接下来创建favorite_food表：

    CREATE TABLE favorite_food
    (
        person_id SMALLINT UNSIGNED,
        food VARCHAR(20),
        CONSTRAINT pk_favorite_food PRIMARY KEY (person_id, food),
        CONSTRAINT fk_fav_food_person_id FOREIGN KEY (person_id) 
            REFERENCES person (person_id)
    );

除了有以下不同外，它与person表的CREATE TABLE语句十分相似：

- 由于一个人可能有多种喜爱的食物，仅靠person_id列不能保证表数据的唯一性，所以本表的主键包含两列：person_id和food。
- favorite_food包含另一种约束类型，即外键约束，它限制了该表的person_id列的值只能够来自于person表的person_id值。

### 2.5 操作与修改数据表

现在有了person表和favorite_food表，下面开始研究使用4中SQL数据语句（INSERT、UPDATE、DELETE和SELECT）。

#### 2.5.1 插入数据

首先讨论INSERT语句，以下是INSERT语句的3个主要组成部分：

- 所要插入数据的表的名称
- 表中需要使用的列的名称
- 需要插入到列的值

实际上向表中增加数据并不需要覆盖所有的列，除非表中所有列都被定义为NOT NULL。

**生成数字型主键数据：**
在向person表中插入数据之前，先讨论一下数字型主键的生成机制是很有帮助的。除了随机选择数字之外，还可以有以下两个常用的选择：

- 查看表中当前主键的最大值，并加1。
- 让数据库服务器自动提供。

*尽管第一种选择看起来很有效，但在多用户环境下可能会发生错误，因为两个用户或许会在同一时间访问表，并产生两个相同的主键值。*实际上，市面上所有的数据库服务器都提供了一个安全、健壮的产生数字型主键的方法。例如Oracle使用独立的方案对象，即序列号（sequence）；而在MySQL中，只需要简单地为主键列打开自增（auto-increment）特性。*通常来说，应该在创建表时就进行此项工作。*下面使用ALTER TABLE命令来修改表的定义：

    -- 去掉favorite_food的外键约束
    ALTER TABLE favorite_food DROP FOREIGN KEY fk_fav_food_person_id;
    -- 对表person的person_id列增加自增特性
    ALTER TABLE person MODIFY person_id SMALLINT UNSIGNED AUTO_INCREMENT;
    -- 重新建立外键约束
    ALTER TABLE favorite_food ADD CONSTRAINT fk_fav_food_person_id FOREIGN KEY (person_id) REFERENCES person (person_id);

由于表favorite_food的外键引用的是表person的person_id列，所以在对其进行修改前需要先去除外键约束，否则会发生报错：

    Error Code: 1833
    Cannot change column 'person_id': used in a foreign key constraint 'fk_fav_food_person_id' of table 'bank.favorite_food'

重新使用DESC person会发现在person_id的Extra列出现了auto_increment特性。此时再向person表插入数据时，可以简单地将person_id列赋值为NULL，MySQL会自动向该列提供下一个可用数字（默认从1开始）。

**INSERT语句：**

下面的语句在person表中插入一条数据：

    INSERT INTO person (fname, lname, gender, birth_date) VALUES ('William', 'Turner', 'M', '1972-05-27');

插入成功后，可以使用SELECT语句进行查询：

    SELECT person_id, fname, lname, gender, birth_date FROM person;

返回结果：

person_id|fname|lname|gender|birth_date
-----|-----|-----|-----|-----
1|William|Turner|M|1972-05-27    

此处省略了查询条件，只是简单地获取表中的所有数据。如果表中数据多于1行，那么可以使用WHERE子句指定想要提取的数据，示例：

    SELECT person_id, fname, lname, gender, birth_date FROM person WHERE person_id = 1;

返回结果相同，因为此时表中只有`person_id = 1`一条数据。

上述INSERT语句中需要注意的几个地方：

- 并没有为任何地址列赋值。这没有关系，因为这些列允许为NULL。
- 为birth_date列提供的值为字符串，但只要符合格式，MySQL会自动将字符串转换为日期类型。
- INSERT语句中列出的字段数量必须与VALUES中的值的数量匹配，并且VALUES中的值必须能够被转换为对应列所要求的数据类型。

下面为表favorite_food插入William Turner最喜欢的食物：

    INSERT INTO favorite_food VALUES(1, 'pizza');
    INSERT INTO favorite_food VALUES(1, 'cookie');
    INSERT INTO favorite_food VALUES(1, 'nachos');

如果我们不知道William Turner的主键是多少，那么可以在INSERT语句中利用其姓名（如果姓名唯一的话）进行查询：

    INSERT INTO favorite_food SELECT p.person_id, 'noodles' FROM person p WHERE p.fname = 'William' AND p.lname = 'Turner';

上述语句相当于：`INSERT INTO favorite_food VALUES(1, 'noodles');`

下面为person表再添加一个记录：

    INSERT INTO person VALUES (NULL, 'Susan', 'Smith', 'F', '1975-11-02', '23 Maple St.', 'Arlington', 'VA', 'USA', '20220');

再次查询可以看到此时她的person_id为2。

**获取XML格式的数据：**

如果想要将查询的结果转为XML格式，可以在调用mysql命令行时使用`--xml`选项，这样查询的输出将会自动转为XML格式。

示例：

    mysql -u lrngsql -p --xml bank

登录后进行查询：

    select * from person;

返回结果：

    <?xml version="1.0"?>
        <resultset statement="select * from person;" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <row>
            <field name="person_id">1</field>
            <field name="fname">William</field>
            <field name="lname">Turner</field>
            <field name="gender">M</field>
            <field name="birth_date">1972-05-27</field>
            <field name="street" xsi:nil="true" />
            <field name="city" xsi:nil="true" />
            <field name="state" xsi:nil="true" />
            <field name="country" xsi:nil="true" />
            <field name="postal_code" xsi:nil="true" />
        </row>
        <row>
            <field name="person_id">2</field>
            <field name="fname">Susan</field>
            <field name="lname">Smith</field>
            <field name="gender">F</field>
            <field name="birth_date">1975-11-02</field>
            <field name="street">23 Maple St.</field>
            <field name="city">Arlington</field>
            <field name="state">VA</field>
            <field name="country">USA</field>
            <field name="postal_code">20220</field>
        </row>
    </resultset>

#### 2.5.2 更新数据

下面更新William Turner的地址数据：

    UPDATE person SET street = '1225 tremont St.', city = 'Boston', state = 'MA', country = 'USA', postal_code = '02138' WHERE person_id = 1;

主键在这里被用于识别数据。根据WHERE子句的条件，在单个语句中修改多个数据行是可行的。例如`WHERE person_id < 10`，此时会修改person_id小于10的所有记录。

#### 2.5.3 删除数据 

使用DELETE删除Susan：

    DELETE FROM person WHERE person_id = 2;

同样地，主键再次被用于识别要操作的记录。与UPDATE类似，根据WHERE子句中的条件，同时删除多行是允许的，如果省略WHERE子句，则会删除表中的所有行。

### 2.6 导致错误的语句

到目前为止，上述所有的SQL语句都复合标准格式并且运行良好。但对于person和favorite_food的表定义来说，在删除或修改数据时可能会出现很多运行错误，以下为一些常见错误，以及MySQL服务器是如何对它们进行响应的。

#### 2.6.1 主键不唯一

由于表定义中创建了主键约束，所以MySQL将会确保重复主键不会被插入到数据表中。

示例：

    INSERT INTO person (person_id, fname, lname, gender, birth_date) VALUES (1, 'Charles', 'Fulton', 'M', '1968-01-15');

发生报错：

    Error Code: 1062
    Duplicate entry '1' for key 'PRIMARY'

这里只要保证person_id列的值不同，就可以创建两个具有完全相同的姓名、地址、出生日期等条目的记录，至少在当前方案对象中是不受限制的。

#### 2.6.2 不存在的外键

favorite_food的表定义了在person_id列上的外键，该约束确保favorite_food表中所输入的person_id列的值都存在与person表的person_id中。

示例：

    INSERT INTO favorite_food VALUES (2, 'lasagna');

发生报错：

    Error Code: 1452
    Cannot add or update a child row: a foreign key constraint fails (`bank`.`favorite_food`, CONSTRAINT `fk_fav_food_person_id` FOREIGN KEY (`person_id`) REFERENCES `person` (`person_id`))

在此情况下，由于favorite_food表的部分数据依赖于person表，因此可以将favorite_food表视为子表，而将person表视为父表，如果需要同时向两个表中输入数据，那么在向favorite_food表输入之前，必须先在父表中创建一行。

*外键约束只能在使用InnoDB存储引擎创建表时起作用。*

#### 2.6.3 列值不合法

person表中的gender列不限制只能插入‘M’或‘F’，在插入其它值时会发生报错。

示例：

    INSERT INTO person (person_id, fname, lname, gender, birth_date) VALUES (2, 'Charles', 'Fulton', 'X', '1968-01-15');

发生报错：

    Error Code: 1265
    Data truncated for column 'gender' at row 1

#### 2.6.4 无效的日期转换

如果构建一个用于日期列的字符串，那么该字符串必须符合要求的格式，否则将会发生报错。

示例：

    UPDATE person SET birth_date = 'DEC-21-1980' WHERE person_id = 1;

发生报错：

    Error Code: 1292
    Incorrect date value: 'DEC-21-1980' for column 'birth_date' at row 1

通常情况下，显式的指定字符串格式是比依赖默认格式更好的做法。

示例：

    UPDATE person SET birth_date = STR_TO_DATE('DEC-21-1980', '%b-%d-%Y') WHERE person_id = 1;

这里使用了STR_TO_DATE函数对指定格式的字符串转换为日期。

下面为MySQL中将字符串转换为DATETIME类型时常用的格式：

- %a：星期几的简写，比如Sun、Mon
- %b：月名称的简写，比如Jan、Feb
- %c：月份的数字形式（0..12）
- %d：日在月中的次序（00..31）
- %f：毫秒数（000000..999999）
- %H：24时格式中的小时（00..23）
- %h：12时格式中的小时（01..12）
- %i：小时中的分钟（00..59）
- %j：一年中天的次序（001..366）
- %M：完整的月名称（January..December）
- %m：月份的数字表示
- %p：AM或PM
- %s：秒数（00..59）
- %W：完整的星期名（Sunday..Saturday）
- %w：天在星期中的次序（0 = 周日..6 = 周六）
- %Y：4位数字的年份

### 2.7 Bank方案

之后的章节所有数据表均来自数据库bank。

bank库中的定义：

表名|定义
-----|-----
account | 为特定顾客开放的特定产品
branch | 开展银行交易业务的场所
business | 公司顾客（customer表的子类型）
customer | 与银行有业务往来的个人或公司
department | 执行特定银行职能的雇员分组
employee | 银行的工作人员
individual | 个人顾客（customer表的子类型）
officer | 允许为公司顾客发起商务交易的人
product | 向顾客提供的银行服务
product_type | 具备相似功能的产品的分组
transaction | 改变账户余额的操作

最后，我们删除person表和favorite表：

    DROP TABLE favorite_food, person;

## 第三章 查询入门

>
本章讨论SELECT语句的各个组成部分，以及它们之间是如何相互作用的。

### 3.1 查询机制

在解析SELECT语句之前，我们应该先了解一下MySQL服务器执行查询的机制。

在使用用户名和密码（远程主机还需要提供主机名）登录MySQL服务器后，服务器会为用户创建一个数据库连接。该连接从应用程序发出请求后会一直保持，直到应用程序释放连接，比如输入quit命令或服务器关机。MySQL服务器会为每个连接赋予一个标识符，它会在首次登录时显示：

    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 5
    Server version: 5.7.19-log MySQL Community Server (GPL)
    ...
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

此处标识符为5。当发生异常情况时，该信息可以给DBA带来帮助，例如手动结束运行几个小时的有问题的查询。

在服务器验证完用户名和密码，并且创建连接后，用户就可以执行查询了。每当查询被发送到服务端时，服务器在执行语句之前将会进行下面的检查。

- 用户是否有权限执行该语句
- 用户是否有权限访问目标数据
- 语句的语法是否正确

如果查询语句通过了这3个测试，就会被传递给查询优化器，他负责为查询找到最有效率的执行方式。优化器通常会做诸如确定FROM子句后面各表的连接顺序，或是可以使用哪些索引之类的工作，然后选择一个执行方案，以供服务器执行该查询。

当服务器执行完查询后，将会调用程序返回一个结果集。

### 3.2 查询语句

SELECT语句是由几个组件或者说子句构成。不过在MySQL中，只有一种子句是必不可少的（SELECT），通常的查询语句会至少包含6个子句中的2 ~ 3个。

query子句：

子句名称 | 使用目的
-----|-----
SELECT | 确定结果集中应该包含哪些列
FROM | 指明所要提取数据的表，以及这些表是如何连接的
WHERE | 过滤不需要的数据
GROUP BY | 用于对具有相同列值的行进行分组
HAVING | 过滤掉不需要的组
ORDER BY | 按一个或多个列，对最后的结果集中的行进行排序

上述子句都数于ANSI标准，此外MySQL还有一些特有的子句，这部分将在附录B中说明。

### 3.3 SELECT子句

尽管SELECT子句是SELECT语句中的一个组成部分，但实际上在数据库服务中，它是最后被评估的。因为在确定结果集最后包含哪些列之前，必须先要知道结果集所有可能包含的列。因此为了更好地理解SELECT子句的角色，首先需要了解一下FROM子句。

示例：

    SELECT * FROM department;

返回结果：

dept_id|name
-----|-----
1|Operations
2|Loans
3|Administration

在此查询中，FROM子句只列出了一个表department，并且SELECT子句指示在结果集中需要包含该表的所有列。

当然也可以只选择表中各列的一个子集：

    SELECT NAME FROM department;

返回数据：

<table>
    <tr>
        <td>name</td>
    </tr>
    <tr>
        <td>Operations</td>
    </tr>
    <tr>
        <td>Loans</td>
    </tr>
    <tr>
        <td>Administration</td>
    </tr>    
</table>

SELECT子句的作用概括如下：**SELECT子句用于在所有可能的列中，选择查询结果集要包含哪些列。**

SELECT子句不只是能返回FROM子句后面各表中实际存在的列，SELECT子句也可以包含以下内容：

- 字符，比如数字或字符串
- 表达式，比如transaction.amout * -1
- 调用内建函数，比如ROUND(transaction.amout, 2)
- 用户自定义的函数调用

示例：

    SELECT emp_id, 'ACTIVE', emp_id * 3.14159, UPPER(lname) FROM employee;

返回结果：

emp_id|ACTIVE|emp_id * 3.14159|upper(lname)
-----|-----|-----|-----
1|ACTIVE|3.14159|SMITH
2|ACTIVE|6.28318|BARKER
3|ACTIVE|9.42477|TYLER
4|ACTIVE|12.56636|HAWTHORNE
5|ACTIVE|15.70795|GOODING
6|ACTIVE|18.84954|FLEMING
7|ACTIVE|21.99113|TUCKER
8|ACTIVE|25.13272|PARKER
9|ACTIVE|28.27431|GROSSMAN
10|ACTIVE|31.41590|ROBERTS
11|ACTIVE|34.55749|ZIEGLER
12|ACTIVE|37.69908|JAMESON
13|ACTIVE|40.84067|BLAKE
14|ACTIVE|43.98226|MASON
15|ACTIVE|47.12385|PORTMAN
16|ACTIVE|50.26544|MARKHAM
17|ACTIVE|53.40703|FOWLER
18|ACTIVE|56.54862|TULMAN

如果只是执行一个内建函数或对简单的表达式求值，则可以完全省略FROM子句。

示例：

    SELECT VERSION(), USER(), DATABASE();

version()|user()|database()
-----|-----|-----
5.7.19-log|lrngsql@localhost|bank

上述查询只是简单地调用了3个内建函数，并没有从任何表中获取数据，因此不需要使用FROM子句。

#### 3.3.1 列的别名

尽管在查询时MySQL为查询所返回的每个列都提供了默认标签，但我们也可以使用自定义标签

示例：

    SELECT emp_id, 'ACTIVE' STATUS, emp_id * 3.14159 empid_x_pi, UPPER(lname) last_name_upper FROM employee;

返回结果：

emp_id|status|empid_x_pi|last_name_upper
-----|-----|-----|-----
1|ACTIVE|3.14159|SMITH
2|ACTIVE|6.28318|BARKER
3|ACTIVE|9.42477|TYLER
4|ACTIVE|12.56636|HAWTHORNE
5|ACTIVE|15.70795|GOODING
6|ACTIVE|18.84954|FLEMING
7|ACTIVE|21.99113|TUCKER
8|ACTIVE|25.13272|PARKER
9|ACTIVE|28.27431|GROSSMAN
10|ACTIVE|31.41590|ROBERTS
11|ACTIVE|34.55749|ZIEGLER
12|ACTIVE|37.69908|JAMESON
13|ACTIVE|40.84067|BLAKE
14|ACTIVE|43.98226|MASON
15|ACTIVE|47.12385|PORTMAN
16|ACTIVE|50.26544|MARKHAM
17|ACTIVE|53.40703|FOWLER
18|ACTIVE|56.54862|TULMAN

返回结果中第2 ~ 4列都显示了有意义的名字，而不是简单地显示产生该列的函数或表达式的名字，这是因为SELECT子句中为它们分别指定了别名：status、empid_x_pi和last_name_upper。

**使用这些别名使输出的结果更加易于理解，同时如果不是使用mysql工具，而是在Java或C#中进行查询，这样也更易于编程实现。**

为了在子句中更加清晰地表示别名，可以分别在别名前增加关键字AS，示例：

    SELECT emp_id, 
           'ACTIVE' AS STATUS, 
           emp_id * 3.14159 AS empid_x_pi, 
           UPPER(lname) AS last_name_upper 
    FROM employee;

#### 3.3.2 去除重复的行

在某些情况下，查询可能会返回重复的行数据，比如提取所有account表的cust_id：

    SELECT cust_id FROM account;

返回数据：

<table>
<tr>
<td bgcolor=silver class='medium'>cust_id</td></tr>
<tr>
<td class='normal' valign='top'>1</td>
</tr>
<tr>
<td class='normal' valign='top'>1</td>
</tr>
<tr>
<td class='normal' valign='top'>1</td>
</tr>
<tr>
<td class='normal' valign='top'>2</td>
</tr>
<tr>
<td class='normal' valign='top'>2</td>
</tr>
<tr>
<td class='normal' valign='top'>3</td>
</tr>
<tr>
<td class='normal' valign='top'>3</td>
</tr>
<tr>
<td class='normal' valign='top'>4</td>
</tr>
<tr>
<td class='normal' valign='top'>4</td>
</tr>
<tr>
<td class='normal' valign='top'>4</td>
</tr>
<tr>
<td class='normal' valign='top'>5</td>
</tr>
<tr>
<td class='normal' valign='top'>6</td>
</tr>
<tr>
<td class='normal' valign='top'>6</td>
</tr>
<tr>
<td class='normal' valign='top'>7</td>
</tr>
<tr>
<td class='normal' valign='top'>8</td>
</tr>
<tr>
<td class='normal' valign='top'>8</td>
</tr>
<tr>
<td class='normal' valign='top'>9</td>
</tr>
<tr>
<td class='normal' valign='top'>9</td>
</tr>
<tr>
<td class='normal' valign='top'>9</td>
</tr>
<tr>
<td class='normal' valign='top'>10</td>
</tr>
<tr>
<td class='normal' valign='top'>10</td>
</tr>
<tr>
<td class='normal' valign='top'>11</td>
</tr>
<tr>
<td class='normal' valign='top'>12</td>
</tr>
<tr>
<td class='normal' valign='top'>13</td>
</tr>
</table>

因为某些客户可能会具有多个账户，所以查询结果中多次包含了该客户的ID。如果我们只需要对每个客户显示一次ID，而不是为account表中的每一行都显示相应的客户ID，这时可以在SELECT子句中加入DISTINCT关键字。

例如：

    SELECT DISTINCT cust_id FROM account;

返回结果：

<table>
<tr>
<td bgcolor=silver class='medium'>cust_id</td></tr>
<tr>
<td class='normal' valign='top'>1</td>
</tr>
<tr>
<td class='normal' valign='top'>2</td>
</tr>
<tr>
<td class='normal' valign='top'>3</td>
</tr>
<tr>
<td class='normal' valign='top'>4</td>
</tr>
<tr>
<td class='normal' valign='top'>5</td>
</tr>
<tr>
<td class='normal' valign='top'>6</td>
</tr>
<tr>
<td class='normal' valign='top'>7</td>
</tr>
<tr>
<td class='normal' valign='top'>8</td>
</tr>
<tr>
<td class='normal' valign='top'>9</td>
</tr>
<tr>
<td class='normal' valign='top'>10</td>
</tr>
<tr>
<td class='normal' valign='top'>11</td>
</tr>
<tr>
<td class='normal' valign='top'>12</td>
</tr>
<tr>
<td class='normal' valign='top'>13</td>
</tr>
</table>

如果在查询中不需要服务器去掉重复的数据，或者确信结果集中不会包含重复记录，那么可以指定ALL关键字来代替DISTINCT关键字。*不过事实上，ALL关键字是系统默认的，并不需要显式地列出。*

**警告：注意产生无重复的结果集需要首先对数据进行排序，这对于大的结果集来说是相当耗时的。因此不要为了确保去除重复行二随意地使用DISTINCT关键字。正确的做法应该先了解所用的数据是否可能包含重复行，以减少对DISTINCT关键字不必要的使用。**

### 3.4 FROM子句

**FROM子句定义了查询中所使用的表，以及连接这些表的方式。**

#### 3.4.1 表的概念

存储在数据库中关联行的集合只是表的类型之一。我们可以更广泛的使用这个术语，去除其中蕴含的“被存储的数据”的含义，而仅仅考虑其关联行结合的含义。因此在对“表”的更宽泛的定义下，存在3种类型的表：

- 永久表：使用CREATE TABLE语句创建的表
- 临时表：子查询所返回的表
- 虚拟表：使用CREATE VIEW子句创建的视图

这3种类型的表都可以在FROM子句中使用。下面讨论如何在FROM子句中使用另外两种类型的表。

**
子查询产生的表：
**
子查询指的是包含在另一个查询中的查询。子查询可以出现在SELECT语句中的各个部分并且被包含在圆括号中。在FROM子句内，子查询的作用是根据其它查询子句产生临时表。

示例：

    SELECT e.emp_id, e.fname, e.lname NAME FROM (SELECT emp_id, fname, lname, start_date, title FROM employee) e;

返回结果：

emp_id|fname|name
-----|-----|-----
1|Michael|Smith
2|Susan|Barker
3|Robert|Tyler
4|Susan|Hawthorne
5|John|Gooding
6|Helen|Fleming
7|Chris|Tucker
8|Sarah|Parker
9|Jane|Grossman
10|Paula|Roberts
11|Thomas|Ziegler
12|Samantha|Jameson
13|John|Blake
14|Cindy|Mason
15|Frank|Portman
16|Theresa|Markham
17|Beth|Fowler
18|Rick|Tulman

本例中，针对employee表的子查询返回5个列，而外围的查询获取其中3个列。在外围查询中，使用了别名来引用子查询。*这个子查询的例子并不十分有用。*

**
视图：
**

视图是存储在数据字典中的查询，它的型为表现得像一个表，但实际上并不拥有任何数据。当发出一个对视图的查询时，该查询会被绑定到视图的定义上，以产生最终被执行的查询。

示例：创建一个查询employee表的视图

    CREATE VIEW employee_vw AS 
    SELECT emp_id, fname, lname, YEAR(start_date) start_year 
    FROM employee;

当视图被创建后，并没有产生或存储任何数据，服务器只是简单地保留该查询以供将来使用。

既然视图已经存在了，那么就可以对其发出查询请求：

    SELECT emp_id, start_year FROM employee_vw WHERE emp_id < 10;

返回结果：

emp_id|start_year
-----|-----
1|2001
2|2002
3|2000
4|2002
5|2003
6|2004
7|2004
8|2002
9|2002

*创建视图可能处于各种理由，比如对用户隐藏列、简化数据库设计等。*

#### 3.4.2 表连接

对FROM子句定义的第2中偏差（第1种指的是对表定义的理解）是：如果FROM子句中出现了多个表，那么要求同时包含各表之间的连接条件。**这在MySQL数据库或其它任何数据库中都不是必需的，但却是ANSI所建议的连接多个表的方法，也是在各种数据库服务器之间最具可移植性的操作。**

多表连接问题在后续章节深入讨论，以下是一个示例：

    SELECT employee.emp_id, employee.fname, employee.lname, department.name dept_name 
    FROM employee 
    INNER JOIN department 
    ON employee.dept_id = department.dept_id;


返回数据：

emp_id|fname|lname|dept_name
-----|-----|-----|-----
4|Susan|Hawthorne|Operations
6|Helen|Fleming|Operations
7|Chris|Tucker|Operations
8|Sarah|Parker|Operations
9|Jane|Grossman|Operations
10|Paula|Roberts|Operations
11|Thomas|Ziegler|Operations
12|Samantha|Jameson|Operations
13|John|Blake|Operations
14|Cindy|Mason|Operations
15|Frank|Portman|Operations
16|Theresa|Markham|Operations
17|Beth|Fowler|Operations
18|Rick|Tulman|Operations
5|John|Gooding|Loans
1|Michael|Smith|Administration
2|Susan|Barker|Administration
3|Robert|Tyler|Administration

上面的查询需要同时显示employee表（emp_id、fname、lname）和department表（name），因此两个表都包含在FROM子句中。两个表的连接方式为INNER JOIN。两个表的连接条件为ON employee.dept_id = department.dept_id。

#### 3.4.3 定义表别名

当在单个查询中连接多个表时，需要在SELECT、WHERE、GROUP BY、HAVING以及ORDER BY子句中指明所引用的表。有两种在FROM子句之外引用表的方式：

- 使用完整的表名称，如employee.emp_id。
- 为每个表指定别名，并在查询中需要的地方使用该别名。

对上一节的连接查询使用表别名实现：

    SELECT e.emp_id, e.fname, e.lname, d.name dept_name 
    FROM employee e 
    INNER JOIN department d 
    ON e.dept_id = d.dept_id;

也可以在别名前使用AS关键字，这与列的别名相同。

    SELECT e.emp_id, e.fname, e.lname, d.name dept_name 
    FROM employee AS e 
    INNER JOIN department AS d 
    ON e.dept_id = d.dept_id;

### 3.5 WHERE子句

**WHERE子句用于在结果集中过滤掉不需要的行。**

示例：查询employee表，但只获取title为head teller的雇员数据

    SELECT emp_id, fname, lname, start_date, title FROM employee WHERE title = 'head teller';

返回数据：

emp_id|fname|lname|start_date|title
-----|-----|-----|-----|-----
6|Helen|Fleming|2008-03-17|Head Teller
10|Paula|Roberts|2006-07-27|Head Teller
13|John|Blake|2000-05-11|Head Teller
16|Theresa|Markham|2001-03-15|Head Teller

如果要同时使用更多的条件，它们之间使用操作符AND、OR或者NOT分隔。

示例：在上述条件下查询2006年1月之后入职的员工

    SELECT emp_id, fname, lname, start_date, title FROM employee WHERE title = 'head teller' AND start_date > '2005-12-31';

返回数据：

emp_id|fname|lname|start_date|title
-----|-----|-----|-----|-----
6|Helen|Fleming|2008-03-17|Head Teller
10|Paula|Roberts|2006-07-27|Head Teller

使用AND进行分隔时，需要结果集中的行满足所有条件。而使用OR进行分隔时，只需要满足其中任意一个条件即可。

当同时使用AND和OR时，需要使用圆括号对条件分组。

示例：查询2006加入公司的head teller雇员或在2007年加入公司的teller雇员。

    SELECT emp_id, fname, lname, start_date, title 
    FROM employee 
    WHERE 
    (title = 'head teller' AND start_date > '2005-12-31') 
    OR 
    (title = 'teller' AND start_date > '2006-12-31');

**在混合使用不同的操作符时，开发者应当总是使用圆括号来分隔成组的条件，这样确保开发者、数据库服务器以及在以后可能会修改代码的其他开发者都能够对其意义保持一致的理解。**

### 3.6 GROUP BY和HAVING子句

前述的查询仅仅提取数据而未对它们进行任何加工。不过有时候也会需要数据库服务器在返回结果集之前对数据进行提炼，其中一种方式是使用GROUP BY子句，它用于根据列值对数据进行分组。例如，我们并不需要查看雇员和他们所处部门的列表，而是想要获取不嫩和它所拥有雇员数量的清单。

在使用GROUP BY子句时，可能还需要同时使用HAVING子句，他能够以与WHERE子句类似的方式对分组数据进行过滤。

示例：查询每个部门的雇员总数，并返回雇员数大于2的部门名称及他们的雇员数

    SELECT d.name, COUNT(e.emp_id) num_employee 
    FROM department d 
    INNER JOIN employee e 
    ON d.dept_id = e.dept_id 
    GROUP BY d.name 
    HAVING COUNT(e.emp_id) > 2;

后续的章节回对GROUP BY和HAVING子句进行完整的讨论。

### 3.7 ORDER BY子句

通常情况下，查询的结果集返回的行并不以特定的顺序排列。如果需要对他们进行排列，则可以使用ORDER BY子句。

**ORDER BY子句用于对结果集中的原始列数据或是根据列数据计算的表达式结果进行排序。**

示例：查询emp_id，title以及start_date，并先按照start_date排序，再按照emp_id排序

    SELECT emp_id, title, start_date FROM employee ORDER BY start_date, emp_id;

返回结果：

emp_id|title|start_date
-----|-----|-----
3|Treasurer|2000-02-09
13|Head Teller|2000-05-11
11|Teller|2000-10-23
16|Head Teller|2001-03-15
1|President|2001-06-22
4|Operations Manager|2002-04-24
9|Teller|2002-05-03
17|Teller|2002-06-29
14|Teller|2002-08-09
2|Vice President|2002-09-12
8|Teller|2002-12-02
18|Teller|2002-12-12
5|Loan Manager|2003-11-14
10|Head Teller|2006-07-27
12|Teller|2007-01-08
15|Teller|2007-04-01
6|Head Teller|2008-03-17
7|Teller|2008-09-15

*ORDER BY子句中各列的出现顺序决定了各列进行排序的次序。*

#### 3.7.1 升序或降序排序

在排序时，可以通过关键字ASC和DESC指定是升序还是降序。默认情况下是升序排列，因此只需要在想要降序排序时加上DESC关键字即可。

示例：

    SELECT account_id, product_cd, open_date, avail_balance 
    FROM account 
    WHERE avail_balance > 5000.00 
    ORDER BY avail_balance DESC;

返回结果：

account_id|product_cd|open_date|avail_balance
-----|-----|-----|-----
29|SBL|2004-02-22|50000.00
28|CHK|2003-07-30|38552.05
24|CHK|2002-09-30|23575.12
15|CD|2004-12-28|10000.00
22|MM|2004-10-28|9345.55
27|BUS|2004-03-22|9345.55
12|MM|2004-09-30|5487.09

降序排序通常用于排行查询，例如“查询余额最高的5个账户”。MySQL的LIMIT子句允许对查询的结果进行过滤，只显示其中的前X行。

#### 3.7.2 根据表达式排序

使用列数据对结果集进行排序十分有用，但有时或许还需要根据一些并非存放在数据库中的，甚至可能没有在查询中出现的内容进行排序，而在ORDER BY子句后增加表达式可以满足该需求。

示例：根据fed_id列的后三位数字进行排序

    SELECT cust_id, cust_type_cd, city, state, fed_id 
    FROM customer 
    ORDER BY RIGHT(fed_id, 3) DESC;

返回数据：

cust_id|cust_type_cd|city|state|fed_id
-----|-----|-----|-----|-----
9|I|Newton|MA|999-99-9999
8|I|Salem|NH|888-88-8888
7|I|Wilmington|MA|777-77-7777
6|I|Waltham|MA|666-66-6666
5|I|Salem|NH|555-55-5555
4|I|Waltham|MA|444-44-4444
13|B|Quincy|MA|04-4444444
3|I|Quincy|MA|333-33-3333
12|B|Salem|NH|04-3333333
2|I|Woburn|MA|222-22-2222
11|B|Wilmington|MA|04-2222222
1|I|Lynnfield|MA|111-11-1111
10|B|Salem|NH|04-1111111

该查询使用内建函数RIGHT()提取fed_id列的最后三个字符，并根据该值对返回的行进行排序

#### 3.7.3 根据占位符排序

如果需要根据SELECT子句中的列来排序，那么可以选择使用该列位于SELECT子句中的位置号来替代列明。

示例：

    SELECT account_id, product_cd, open_date, avail_balance 
    FROM account 
    WHERE avail_balance > 5000.00 
    ORDER BY avail_balance DESC;

上述SQL语句可替换为：

    SELECT account_id, product_cd, open_date, avail_balance 
    FROM account 
    WHERE avail_balance > 5000.00 
    ORDER BY 4 DESC;

这种做法可能并不常见，**因为如果在SELECT子句中增加了新的列，而没有同步改变ORDER BY子句中的序号，就可能导致预料之外的结果。***所以建议在单独查询中可以使用数字序号，而在编写程序时则使用名称来引用列。*

## 第四章 过滤

>
本章将讨论SELECT、UPDATE和DELETE语句中的WHERE子句所能使用的各种类型的过滤条件。

大多数情况下，查询所关注的只是表中所有行的一个子集。因此，所有的SQL数据处理语句（insert除外）都包含了可选的WHERE子句，其中的过滤条件限制SQL语句所需要的行数。除此之外，SELECT语句中包含的HAVING子句也可以对分组数据进行条件过滤。对于HAVING子句中过滤条件的使用将在后续章节中讨论。

### 4.1 条件评估

WHERE子句可能包含一个或多个条件，每个条件之间使用操作符AND或OR分隔。只有当WHERE子句返回TRUE时，相应的行才可以被包含在结果集中。

**使用AND的两条件评估：**

中间结果|最终结果
-----|-----
WHERE TRUE AND TRUE | TRUE
WHERE TRUE AND FALSE | FALSE
WHERE FALSE AND TRUE | FALSE
WHERE FALSE AND FALSE | FALSE

**使用OR的两条件评估：**

中间结果|最终结果
-----|-----
WHERE TRUE OR TRUE | TRUE
WHERE TRUE OR FALSE | TRUE
WHERE FALSE OR TRUE | TRUE
WHERE FALSE OR FALSE | FALSE

#### 4.1.1 使用圆括号

如果WHERE子句包含了三个或更多的条件，且同时使用了AND或OR操作符，那么需要使用圆括号来明确意图，以使数据库服务器或者以后可能阅读你代码的其他开发者能够理解。

示例：

    WHERE end_date IS NULL AND (title = 'Teller' OR start_date < '2007-01-01')

**使用AND、OR的三条件评估**

中间结果|最终结果
-----|-----
WHERE TRUE AND (TRUE OR TRUE) | TRUE
WHERE TRUE AND (TRUE OR FALSE) | TRUE
WHERE TRUE AND (FALSE OR TRUE) | TRUE
WHERE TRUE AND (FLASE OR FALSE) | FALSE
WHERE FALSE AND (TRUE OR TRUE) | FALSE
WHERE FALSE AND (TRUE OR FALSE) | FALSE
WHERE FALSE AND (FALSE OR TRUE) | FALSE
WHERE FALSE AND (FLASE OR FALSE) | FALSE

#### 4.1.2 使用NOT操作符

NOT操作符会反转条件判断的结果。

示例：

    WHERE end_date IS NULL AND NOT (title = 'Teller' OR start_date < '2007-01-01')

当使用了NOT操作符后意味着只有当条件`(title = 'Teller' OR start_date < '2007-01-01')`返回FALSE时`NOT (FALSE)`才返回TRUE。

*尽管对于数据库服务器来说，处理包含NOT操作符的WHERE子句并不费力，但对于开发者来说，这增加了对条件评估的困难，这也是通常情况下较少使用它的原因。*

上例中的WHERE子句可以重写为以下形式：

    WHERE end_date IS NULL AND (title != 'Teller' and start_date >= '2007-01-01')

### 4.2 构建条件

条件通常由1个或者多个包含1个到多个操作符的表达式构成。表达式可以是下面类型中的任意一个：

- 数字
- 表或视图中的列
- 字符串
- 内建函数
- 子查询
- 表达式列表
- 比较操作符
- 算数操作符

### 4.3 条件类型

有多种方式可以过滤掉不需要的数据，比如通过指定特定值、值集合、需要包含或排除的值的范围，以及在针对字符串数据时，使用各种模式搜索技术来查找部分匹配。

#### 4.3.1 相等条件

类似于`column = expression`形式的条件被称为相等条件。

下面的查询使用两个相等条件：其中一个在ON子句中（连接条件），另一个在WHERE子句中（过滤条件）。

示例：

    SELECT pt.name product_type, p.name product 
    FROM product p 
    INNER JOIN product_type pt 
    ON p.product_type_cd = pt.product_type_cd 
    WHERE pt.name = 'Customer Accounts';

返回数据：

product_type|product
-----|-----
Customer Accounts|certificate of deposit
Customer Accounts|checking account
Customer Accounts|money market account
Customer Accounts|savings account

**不等条件：**
另一种比较常见的条件类型是不等调教，它用于判断两个表达式不想等。

示例：

    SELECT pt.name product_type, p.name product 
    FROM product p 
    INNER JOIN product_type pt 
    ON p.product_type_cd = pt.product_type_cd 
    WHERE pt.name <> 'Customer Accounts';

返回数据：

product_type|product
-----|------
Individual and Business Loans|auto loan
Individual and Business Loans|business line of credit
Individual and Business Loans|home mortgage
Individual and Business Loans|small business loan

在构造不相等条件时可以在‘!=’和‘<>’中任选一个。

**使用相等条件修改数据：**
在修改数据时经常要用到相等/不等条件。例如要删除account表中在20002年关闭的账户。

示例：

    DELETE FROM account WHERE STATUS = 'CLOSE' AND YEAR(close_date) = 2002;

#### 4.3.2 范围条件

除了可以检查表达式与另一个表达式是否相等以外，还可以构建条件来检查表达式的值是否处于某个区间。这种类型的条件通常用于数值型或临时数据。

示例：

    SELECT emp_id, fname, lname, start_date FROM employee WHERE start_date < '2007-01-01';

返回数据：

emp_id|fname|lname|start_date
-----|-----|-----|-----
1|Michael|Smith|2001-06-22
2|Susan|Barker|2002-09-12
3|Robert|Tyler|2000-02-09
4|Susan|Hawthorne|2002-04-24
5|John|Gooding|2003-11-14
8|Sarah|Parker|2002-12-02
9|Jane|Grossman|2002-05-03
10|Paula|Roberts|2006-07-27
11|Thomas|Ziegler|2004-10-23
13|John|Blake|2004-05-11
14|Cindy|Mason|2006-08-09
16|Theresa|Markham|2005-03-15
17|Beth|Fowler|2006-06-29
18|Rick|Tulman|2006-12-12

指定上限的同时也可以指定下限：

示例：

    SELECT emp_id, fname, lname, start_date FROM employee 
    WHERE start_date < '2007-01-01' AND start_date >= '2005-01-01';

返回数据：

emp_id|fname|lname|start_date
-----|-----|-----|-----
1|Michael|Smith|2005-06-22
2|Susan|Barker|2006-09-12
3|Robert|Tyler|2005-02-09
4|Susan|Hawthorne|2006-04-24
8|Sarah|Parker|2006-12-02
9|Jane|Grossman|2006-05-03
10|Paula|Roberts|2006-07-27
14|Cindy|Mason|2006-08-09
16|Theresa|Markham|2005-03-15
17|Beth|Fowler|2006-06-29
18|Rick|Tulman|2006-12-12

**BETWEEN操作符：**
当需要同时限制范围的上限和下限时，可以选择使用BETWEEN操作符构建一个查询条件，而不需要两个单独的限制条件。

示例：

    SELECT emp_id, fname, lname, start_date 
    FROM employee 
    WHERE start_date BETWEEN '2005-01-01' AND '2007-01-01';

查询结果与前述查询结果相同。

当使用BETWEEN操作符时，需要注意下面的事项，即必须首先指定范围的下限，然后再指定上限。

**字符串范围：**
使用日期和数字的范围是易于理解的，但同样可以使用字符串作为搜索范围的条件。

示例：

    SELECT cust_id, fed_id FROM customer 
    WHERE cust_type_cd = 'I' 
          AND fed_id BETWEEN '500-00-0000' AND '999-99-9999';

返回数据：

cust_id|fed_id
-----|-----
5|555-55-5555
6|666-66-6666
7|777-77-7777
8|888-88-8888
9|999-99-9999

使用字符串范围时，需要知道所使用的字符集中各字符的次序。再某个字符集内个字符的次序被称为排序顺序（collation）。

#### 4.3.3 成员条件

再一些情况下，不是需要限制表达式为特定值或某个范围的值，而是一个有限值的集合。例如，要再account表中查询所有产品代码为'CHK'、'SAV'、'CD'或'MM'的账户。

示例：

    SELECT account_id, product_cd, cust_id, avail_balance 
    FROM account 
    WHERE product_cd = 'CHK' 
          OR product_cd = 'SAV' 
          OR product_cd = 'CD' 
          OR product_cd = 'MM';

返回数据：

account_id|product_cd|cust_id|avail_balance
-----|-----|-----|-----
1|CHK|1|1057.75
2|SAV|1|500.00
3|CD|1|3000.00
4|CHK|2|2258.02
5|SAV|2|200.00
7|CHK|3|1057.75
8|MM|3|2212.50
10|CHK|4|534.12
11|SAV|4|767.77
12|MM|4|5487.09
13|CHK|5|2237.97
14|CHK|6|122.37
15|CD|6|10000.00
17|CD|7|5000.00
18|CHK|8|3487.19
19|SAV|8|387.99
21|CHK|9|125.67
22|MM|9|9345.55
23|CD|9|1500.00
24|CHK|10|23575.12
28|CHK|12|38552.05

此时可以使用IN操作符，无论集合中有多少个表达式，使用IN操作符都只需要编写一个条件。

示例：

    SELECT account_id, product_cd, cust_id, avail_balance 
    FROM account 
    WHERE product_cd IN ('CHK', 'SAV', 'CD', 'MM');

查询结果与前述相同。

**使用子查询：**
除了编写自定义的表达式集合，如`('CHK', 'SAV', 'CD', 'MM')`之外，还可以使用子查询产生的中间集合。

示例：

    SELECT account_id, product_cd, cust_id, avail_balance 
    FROM account 
    WHERE product_cd IN 
    (SELECT product_cd FROM product WHERE product_type_cd = 'ACCOUNT');

返回数据：

account_id|product_cd|cust_id|avail_balance
-----|-----|-----|-----
3|CD|1|3000.00
15|CD|6|10000.00
17|CD|7|5000.00
23|CD|9|1500.00
1|CHK|1|1057.75
4|CHK|2|2258.02
7|CHK|3|1057.75
10|CHK|4|534.12
13|CHK|5|2237.97
14|CHK|6|122.37
18|CHK|8|3487.19
21|CHK|9|125.67
24|CHK|10|23575.12
28|CHK|12|38552.05
8|MM|3|2212.50
12|MM|4|5487.09
22|MM|9|9345.55
2|SAV|1|500.00
5|SAV|2|200.00
11|SAV|4|767.77
19|SAV|8|387.99

这里子查询包含4个值的集合，主查询检查每个产品product_cd列的值是否数于子查询所返回的集合。

**使用NOT IN：**
有时候需要检查一个表达式是否再某个表达式集合中存在，但有时需要检查的是它是否不存在。对于这种情况，可以使用NOT IN操作符：

示例：

    SELECT account_id, product_cd, cust_id, avail_balance 
    FROM account 
    WHERE product_cd NOT IN ('CHK', 'SAV', 'CD', 'MM');

返回数据：

account_id|product_cd|cust_id|avail_balance
-----|-----|-----|-----
25|BUS|10|0.00
27|BUS|11|9345.55
29|SBL|13|50000.00

#### 4.3.4 匹配条件

最后一种条件类型是处理部分字符匹配。

举例来说，我们现在需要找到所有具有以T开头的姓氏的雇员，那么可以使用内建函数截取lname列中第一个字母。

示例：

    SELECT emp_id, fname, lname FROM employee WHERE LEFT(lname, 1) = 'T';

返回数据：

emp_id|fname|lname
-----|-----|-----
3|Robert|Tyler
7|Chris|Tucker
18|Rick|Tulman

尽管LEFT()函数可以满足上述需求，但它不具备较好的灵活性。

使用通配符构建搜索表达式是更好的做法，如下面所述。

**使用通配符：**
当根据部分字符串匹配进行搜索时，感兴趣的项目可能包括：

- 以某个字符开始或结束的字符串
- 包含某个子字符串的字符串
- 在字符串的任意位置包含某个字符的字符串
- 在字符串的任意位置包含某个子字符串的字符串
- 具备特定格式而不关心单个字符的字符串

我们可以使用通配符来构建搜索表达式以定位这些字符串。

通配符：

符号 | 匹配
-----|-----
_ | 正好1个字符
% | 任意数目的字符（包含0）

下划线为单个字符的占位符，百分号则代表多个字符。当使用搜索表达式构建条件时，需要使用LIKE操作符。

示例：

    SELECT emp_id, lname FROM employee WHERE lname LIKE '_a%e%';

返回数据：

emp_id|lname
-----|-----
2|Barker
4|Hawthorne
8|Parker
12|Jameson

`'_a%e%'`表示搜索表达式指定字符串的第二个位置必须为字符a，e必须在其后的任何位置（包括最后一个位置）中出现一次。

搜索表达式示例：

搜索表达式 | 解释
-----|-----
F% | 以F开头的字符串
%t | 以t结尾的字符串
%bas% | 在任意位置包含bas的字符串
\_\_t\_ | 包含4个字符，且第3个字符为t的字符串
\_\_\_-\_\_-\_\_\_\_ | 包含11个字符且第4个和第7个字符是破折号的字符串

示例：

    SELECT cust_id, fed_id FROM customer WHERE fed_id LIKE '___-__-____';

返回数据：

cust_id|fed_id
-----|-----
1|111-11-1111
2|222-22-2222
3|333-33-3333
4|444-44-4444
5|555-55-5555
6|666-66-6666
7|777-77-7777
8|888-88-8888
9|999-99-9999

如果需要处理更为复杂的情况，可以使用多个搜索表达式。

示例：查询所有姓氏以F或G打头的雇员

    SELECT emp_id, fname, lname FROM employee WHERE lname LIKE 'F%' OR lname LIKE 'G%';

返回数据：

emp_id|fname|lname
-----|-----|-----
5|John|Gooding
6|Helen|Fleming
9|Jane|Grossman
17|Beth|Fowler

**使用正则表达式：**
如果带通配符的字符串仍然不能提供足够的灵活性，那么可以使用正则表达式来构造搜索表达式。

示例：同样查询所有姓氏以F或G打头的雇员

    SELECT emp_id, fname, lname FROM employee WHERE lname REGEXP '^[FG]';

返回数据：

emp_id|fname|lname
-----|-----|-----
5|John|Gooding
6|Helen|Fleming
9|Jane|Grossman
17|Beth|Fowler

REGEXP操作符接受一个正则表达式（`'^[FG]'`），并将之应用到操作符的左侧表达式（`lname`）。

### 4.4 NULL

NULL表示值的缺失。举例来说，在雇员离职前，employee表的end_date列没有可赋予的值，因此它应该一直为NULL。当然NULL的使用方式比较灵活。

NULL值可以用于以下场景：

- 没有何时的值
- 值未确定
- 值未定义

当使用NULL时，需要记住：

- 表达式可以为NULL，但不能等于NULL
- 两个NULL值不能判断为相等

为了测试表达式是否为NULL，需要使用NULL操作符，示例：

    SELECT emp_id, fname, lname, superior_emp_id FROM employee WHERE superior_emp_id IS NULL;

返回数据：

emp_id|fname|lname|superior_emp_id
-----|-----|-----|-----
1|Michael|Smith|NULL

这里不能将`superior_emp_id IS NULL`写为`superior_emp_id = NULL`。

如果想要检查列中的数据是否被赋值，可以使用NOT NULL操作符。

示例：

    SELECT emp_id, fname, lname, superior_emp_id 
    FROM employee 
    WHERE superior_emp_id IS NOT NULL AND emp_id <= 10;

返回数据：

emp_id | fname | lname | superior_emp_id
-----|-----|-----|-----
2 | Susan | Barker | 1
3 | Robert | Tyler | 1
4 | Susan | Hawthorne | 3
5 | John | Gooding | 4
6 | Helen | Fleming | 4
7 | Chris | Tucker | 6
8 | Sarah | Parker | 6
9 | Jane | Grossman | 6
10 | Paula | Roberts | 4

## 第五章 多表连接

>
本章关注最简单也是最常用的连接，即内连接（INNER JOIN）。后续章节将讨论所有的连接类型。

### 5.1 什么是连接

尽管对单个表的查询并不罕见，但在应用环境下大多数的查询都需要针对两个、三个甚至更多的表。

示例：先查看employee表和department表的定义

    DESC employee;

返回数据：

Field | Type | Null | Key | Default | Extra
-----|-----|-----|-----|-----|-----
emp_id | smallint(5) unsigned | NO | PRI | NULL | auto_increment
fname | varchar(20) | NO |  | NULL | 
lname | varchar(20) | NO |  | NULL | 
start_date | date | NO |  | NULL | 
end_date | date | YES |  | NULL | 
superior_emp_id | smallint(5) unsigned | YES | MUL | NULL | 
dept_id | smallint(5) unsigned | YES | MUL | NULL | 
title | varchar(20) | YES |  | NULL | 
assigned_branch_id | smallint(5) unsigned | YES | MUL | NULL | 

    DESC department;

返回数据：

Field | Type | Null | Key | Default | Extra
-----|-----|-----|-----|-----|-----
dept_id | smallint(5) unsigned | NO | PRI | NULL | auto_increment
NAME | varchar(20) | NO |  | NULL | 

假设现在需要获取每个雇员的姓名以及它们所在的部门名称，显然此查询需要获取`employee.fname`、`employee.lname`和`department.name`列。但是如何在同一个查询中获取两个表的数据呢？答案的关键在于`employee.dept_id`列，它保存了每个雇员所在的部门ID，更确切的说，`employee.dept_id`是指向`department`表的外键。employee表在定义时使用了外键约束：` CONSTRAINT fk_dept_id FOREIGN KEY (dept_id) REFERENCES department (dept_id)`。该查询将指示服务器使用`employee.dept_id`列作为employee表和department表的桥梁，从而实现在同一查询的结果集中包含来自两个表的列，这种操作被称为连接。

#### 5.1.1 笛卡尔积

最简单的连接方式是直接在FROM子句中加入employee表和department表。

示例：

    SELECT e.fname, e.lname, d.name FROM employee e, department d;

或者，在两个表之间用JOIN隔开

    SELECT e.fname, e.lname, d.name FROM employee e JOIN department d;

返回数据：

fname | lname | name
-----|-----|-----
Michael | Smith | Operations
Michael | Smith | Loans
Michael | Smith | Administration
Susan | Barker | Operations
Susan | Barker | Loans
Susan | Barker | Administration
Robert | Tyler | Operations
Robert | Tyler | Loans
Robert | Tyler | Administration
Susan | Hawthorne | Operations
Susan | Hawthorne | Loans
Susan | Hawthorne | Administration
John | Gooding | Operations
John | Gooding | Loans
John | Gooding | Administration
Helen | Fleming | Operations
Helen | Fleming | Loans
Helen | Fleming | Administration
Chris | Tucker | Operations
Chris | Tucker | Loans
Chris | Tucker | Administration
Sarah | Parker | Operations
Sarah | Parker | Loans
Sarah | Parker | Administration
Jane | Grossman | Operations
Jane | Grossman | Loans
Jane | Grossman | Administration
Paula | Roberts | Operations
Paula | Roberts | Loans
Paula | Roberts | Administration
Thomas | Ziegler | Operations
Thomas | Ziegler | Loans
Thomas | Ziegler | Administration
Samantha | Jameson | Operations
Samantha | Jameson | Loans
Samantha | Jameson | Administration
John | Blake | Operations
John | Blake | Loans
John | Blake | Administration
Cindy | Mason | Operations
Cindy | Mason | Loans
Cindy | Mason | Administration
Frank | Portman | Operations
Frank | Portman | Loans
Frank | Portman | Administration
Theresa | Markham | Operations
Theresa | Markham | Loans
Theresa | Markham | Administration
Beth | Fowler | Operations
Beth | Fowler | Loans
Beth | Fowler | Administration
Rick | Tulman | Operations
Rick | Tulman | Loans
Rick | Tulman | Administration

一共返回54行数据。观察结果集发现，18个雇员组成的集合被重复了3次，每次除了部门名称不同之外，其他数据项都是完全相同的。**这是因为查询没有指定两个表应如何连接造成的。**在此情况下，数据库服务器将产生笛卡尔积，即两张表的所有置换（18个雇员 X 3个部门 = 54个置换）。

上述这种连接被称为交叉连接（cross join），它在实际应用中很少使用。交叉连接将在后续的章节进一步讨论。

#### 5.1.2 内连接

要使上述查询只返回18行，则需要描述两个表是如何关联的。前述中提到`employee.dept_id`列起到连接这两个表的作用，因此需要在FROM子句中增加一个包含此列信息的子句。

示例：

    SELECT e.fname, e.lname, d.name 
    FROM employee e JOIN department d 
    ON e.dept_id = d.dept_id;

返回数据：

fname | lname | name
----- | ----- |-----
Susan | Hawthorne | Operations
Helen | Fleming | Operations
Chris | Tucker | Operations
Sarah | Parker | Operations
Jane | Grossman | Operations
Paula | Roberts | Operations
Thomas | Ziegler | Operations
Samantha | Jameson | Operations
John | Blake | Operations
Cindy | Mason | Operations
Frank | Portman | Operations
Theresa | Markham | Operations
Beth | Fowler | Operations
Rick | Tulman | Operations
John | Gooding | Loans
Michael | Smith | Administration
Susan | Barker | Administration
Robert | Tyler | Administration

返回18行数据，而不是54行。这是ON子句产生的作用，它提示服务器使用dept_id列来连接employee表和department表。例如，employee表中的Susan Hawthorne一行包含的dept_id为1，服务器使用该值查找department表中dept_id为1的行，并获取改行的name列值，即Operations。

**如果一个表中的dept_id列中存在某个值，但该值在另一个表的dept_id列中不存在，那么相关行的连接会失败，在结果集中将会排除包含该值的行。**举例来说，如果department表中的第4行为市场部门，但是employee表中没有雇员在此部分下，那么连接后的结果集中将不会出现市场部门。与之对应的是，如果一些雇员被分配到dept_id为99的部门，但该部门在department表中不存在，那么这些雇员行也会被排除在外。

*如果想要包含其中某个表的所有行，而不考虑每行是否在另一个表中存在匹配，那么可以使用外连接（outer join）*，后续章节会讨论该内容。

在上一个例子中，FROM子句并没有指定连接的类型。通常在对两个表使用内连接时，最好在FROM子句中显式指定连接类型。

示例：

    SELECT e.fname, e.lname, d.name 
    FROM employee e INNER JOIN department d 
    ON e.dept_id = d.dept_id;

**当指定了连接条件，但是没有指定连接类型时，服务器将会默认使用内连接。**

如果连接两个表的列名相同，那么可以使用USING子句替代ON子句。

示例：

    SELECT e.fname, e.lname, d.name 
    FROM employee e INNER JOIN department d 
    USING (dept_id);

上述JOIN ON连接语句也可以使用WHERE重写（不推荐），示例：

    SELECT e.fname, e.lname, d.name 
    FROM employee e, department d 
    WHERE e.dept_id = d.dept_id;

#### 5.1.3 ANSI连接语法

本书在连接表时使用都符合SQL92版本的ANSI SQL标准。所有主流数据库都采用SQL92的连接语法。但由于这些数据库都出现在SQL92标准发布之前，它们同样也容许一些旧语法。

示例：大多数服务器都能识别并处理下面的查询

    SELECT e.fname, e.lname, d.name 
    FROM employee e, department d 
    WHERE e.dept_id = d.dept_id;

返回数据：与之前的查询返回数据相同

fname | lname | name
-----|-----|-----
Susan | Hawthorne | Operations
Helen | Fleming | Operations
Chris | Tucker | Operations
Sarah | Parker | Operations
Jane | Grossman | Operations
Paula | Roberts | Operations
Thomas | Ziegler | Operations
Samantha | Jameson | Operations
John | Blake | Operations
Cindy | Mason | Operations
Frank | Portman | Operations
Theresa | Markham | Operations
Beth | Fowler | Operations
Rick | Tulman | Operations
John | Gooding | Loans
Michael | Smith | Administration
Susan | Barker | Administration
Robert | Tyler | Administration

虽然上述这种语法也可以对表进行连接查询，但ANSI连接语法具备以下优点：

- 连接条件和过滤条件被分割到两个子句（ON子句和WHERE子句），使查询语句更易于理解。
- 每个表之间的连接条件都在它们自己的ON子句中列出，这样不容易错误地忽略某些连接条件。
- 使用SQL92连接语法的查询语句可以在各种数据库服务器中通用，而旧的语法在不同的服务器杀那个的表现可能略有不同。

**SQL92连接语法的优势在同时包含连接和过滤条件的复杂查询中表现得更加明显。**

示例：查询branch表中Woburn支行中所有熟练柜员（2007年以前入职）开设的账户

不使用JOIN ON：

    SELECT a.account_id, 
           a.cust_id, 
           a.open_date, 
           a.product_cd 
    FROM account a, branch b, employee e
    WHERE a.open_emp_id = e.emp_id -- a，e的连接条件
    AND e.start_date < '2017-01-01' -- 过滤条件
    AND e.assigned_branch_id = b.branch_id -- a，b的连接条件
    AND e.title IN ('Teller', 'Head Teller') -- 过滤条件
    AND b.name = 'Woburn Branch' -- 过滤条件 ; 

返回数据：

account_id | cust_id | open_date | product_cd
-----|-----|-----|-----
1 | 1 | 2000-01-15 | CHK
2 | 1 | 2000-01-15 | SAV
3 | 1 | 2004-06-30 | CD
4 | 2 | 2001-03-12 | CHK
5 | 2 | 2001-03-12 | SAV
17 | 7 | 2004-01-12 | CD
27 | 11 | 2004-03-22 | BUS

使用这种查询， 不容易分辨WHERE子句中哪些条件是过滤条件，哪些是连接条件。另外对于使用了何种连接类型也不是显而易见。此外还容易遗漏某些连接条件

下面使用SQL92标准重写查询语句：

    SELECT a.account_id, a.cust_id, a.open_date, a.product_cd 
    FROM account a 
    INNER JOIN employee e ON a.open_emp_id = e.emp_id
    INNER JOIN branch b ON e.assigned_branch_id = b.branch_id
    WHERE e.start_date < '2017-01-01'
    AND e.title IN ('Teller', 'Head Teller')
    AND b.name = 'Woburn Branch';

返回数据相同。

### 5.2 连接3个或更多的表

连接3个表的方法与连接两个表类似。FROM子句中将包含3个表和两种连接类型以及两个ON子句。

示例：查询返回所有的商务账户（账户类型为'B'）的账户ID和税务号码及开设此账户的柜员姓名。

    SELECT a.account_id, c.fed_id, e.fname, e.lname 
    FROM account a
    INNER JOIN customer c ON a.cust_id = c.cust_id
    INNER JOIN employee e ON a.open_emp_id = e.emp_id
    WHERE c.cust_type_cd = 'B';

返回数据：

account_id | fed_id | fname | lname
-----|-----|-----|-----
24 | 04-1111111 | Theresa | Markham
25 | 04-1111111 | Theresa | Markham
27 | 04-2222222 | Paula | Roberts
28 | 04-3333333 | Theresa | Markham
29 | 04-4444444 | John | Blake

**连接的顺序：**

这里我们可以调整FROM子句中连接的顺序，示例：

    ... 
    FROM customer c 
    INNER JOIN account a ON ...
    INNER JOIN employee e ON ...
    WHERE ...

或者

    ... 
    FROM employee e 
    INNER JOIN customer c ON ...
    INNER JOIN account a ON ...
    WHERE ...

调整连接的顺序并不会影响返回的结果集。

**连接的顺序是否重要：**
SQL是一种非过程化语言，也就是说只需要描述要获取的数据库对象，而如何以最好的方式执行查询则由数据库服务器负责。服务器根据所收集的数据库对象信息，在3个表中选择一个作为开始点（这个表被称为驱动表），然后确定连接其他表的顺序。因此在FROM子句中各表出现的顺序并不重要。

不过如果我们希望在查询中以特定的顺序连接各表，那么需要将表按照所需要的顺序排列好，然后指定MySQL中的STRIGHT_JOIN关键字。

示例：使用customer表作为驱动表，然后连接account和employee表

    SELECT STRAIGHT_JOIN a.account_id, c.fed_id, e.fname, e.lname
    FROM customer c 
    INNER JOIN account a ON a.cust_id = c.cust_id
    INNER JOIN employee e ON a.open_emp_id = e.emp_id
    WHERE c.cust_type_cd = 'B';

#### 5.2.1 将子查询结果作为查询表

下面是“查询所有Woburn支行中所有熟练柜员开设的账户”的另一个版本，这里使用子查询进行实现。

示例：

    SELECT a.account_id, a.cust_id, a.open_date, a.product_cd 
    FROM account a 
    INNER JOIN 
        (SELECT emp_id, assigned_branch_id 
        FROM employee 
        WHERE start_date < '2017-01-01'
        AND title IN ('Teller', 'Head Teller')) e
    ON a.open_emp_id = e.emp_id
    INNER JOIN
        (SELECT branch_id
        FROM branch
        WHERE NAME = 'Woburn Branch') b
    ON e.assigned_branch_id = b.branch_id;

返回数据：

account_id | cust_id | open_date | product_cd
-----|-----|-----|-----
1 | 1 | 2000-01-15 | CHK
2 | 1 | 2000-01-15 | SAV
3 | 1 | 2004-06-30 | CD
4 | 2 | 2001-03-12 | CHK
5 | 2 | 2001-03-12 | SAV
17 | 7 | 2004-01-12 | CD
27 | 11 | 2004-03-22 | BUS

第1个子查询的别名为e，它用于查找所有有经验的柜员。第2个子查询的别名为b，它用于查找Woburn支行的ID。
首先account表使用open_emp_id与子查询e相连，然后使用子查询e使用assigned_branch_id与子查询b相连。

*注意：主查询中缺少WHERE子句，因为所有的过滤条件都是针对表employee和表branch的。所以主查询中并不需要任何过滤条件。*

下面单独运行子查询，已查看中间结果：

    SELECT emp_id, assigned_branch_id 
        FROM employee 
        WHERE start_date < '2017-01-01'
        AND title IN ('Teller', 'Head Teller')；

返回结果：

emp_id | assigned_branch_id
-----|-----
6 | 1
7 | 1
8 | 1
9 | 1
10 | 2
11 | 2
12 | 2
13 | 3
14 | 3
15 | 3
16 | 4
17 | 4
18 | 4

    SELECT branch_id
        FROM branch
        WHERE NAME = 'Woburn Branch';

返回结果：

<table>
    <tr>
        <td>branch_id</td>
    </tr>
    <tr>
        <td>2</td>
    </tr>
</table>

#### 5.2.2 连续两次使用同一个表

在连接多个表时，有时候可能需要多次连接同一个表。这时需要在FROM子句中多次引用这个表。为了使之正常工作，我们需要为这个表定义不同的别名，以便服务器能够在各子句中正确地引用它们。

示例：

    SELECT a.account_id,
           e.emp_id,
           b_a.name open_branch,
           b_e.name emp_branch
    FROM account a 
    INNER JOIN branch b_a
    ON a.open_branch_id = b_a.branch_id
    INNER JOIN employee e
    ON a.open_emp_id = e.emp_id
    INNER JOIN branch b_e
    ON e.assigned_branch_id = b_e.branch_id
    WHERE a.product_cd = 'CHK';

该查询先hi了每个checking账户的开户柜员、账户的开户行以及开户柜员当前所在支行。其中branch表被包含了两次，别名分别为b_a和b_e。通过为branch表的每个实例指定不同的别名，服务器能够区分所引用的实例：一个用于连接account表，另一个用于连接employee表。

### 5.3 自连接

不仅可以在同一个查询中多次包含同一个表，还可以对表自身进行连接。

例如，employee表包含了一个指向自身的外键，即指向本表主键（emp_id）的列（superior_emp_id）。该列指向雇员的主管。使用自链接，可以在列出每个雇员姓名的同时列出主管的姓名。

示例：

    SELECT e.emp_id, 
           CONCAT(e.fname, ' ', e.lname) NAME, 
           CONCAT(e_mgr.fname, ' ', e_mgr.lname) mgr_name
    FROM employee e 
    INNER JOIN employee e_mgr
    ON e.superior_emp_id = e_mgr.emp_id

返回数据：

emp_id | name | mgr_name
-----|-----|-----
2 | Susan Barker | Michael Smith
3 | Robert Tyler | Michael Smith
4 | Susan Hawthorne | Robert Tyler
5 | John Gooding | Susan Hawthorne
6 | Helen Fleming | Susan Hawthorne
7 | Chris Tucker | Helen Fleming
8 | Sarah Parker | Helen Fleming
9 | Jane Grossman | Helen Fleming
10 | Paula Roberts | Susan Hawthorne
11 | Thomas Ziegler | Paula Roberts
12 | Samantha Jameson | Paula Roberts
13 | John Blake | Susan Hawthorne
14 | Cindy Mason | John Blake
15 | Frank Portman | John Blake
16 | Theresa Markham | Susan Hawthorne
17 | Beth Fowler | Theresa Markham
18 | Rick Tulman | Theresa Markham

该查询包含两个employee实例，一个用于提供雇员ID和姓名（e），另一个提供主管姓名（e_mgr）。

### 5.4 相等连接和不相等连接

大多数查询使用的都是相等连接，但有时也可以通过限定值的范围实现对表的连接，即不等连接。

示例：假设现在要举办一次面向银行柜员的象棋比赛，要求创建所有对弈者的列表。这就需要为所有柜员（`title = 'Teller'`）进行employee的自链接，并返回所有emp_id不同的行（因无法与自己下棋）。

    SELECT CONCAT(e1.fname, ' ', e1.lname) player1,
           'VS',
           CONCAT(e2.fname, ' ', e2.lname) player2
    FROM employee e1
    INNER JOIN employee e2
    ON e1.emp_id < e2.emp_id
    WHERE e1.title = 'Teller' AND e2.title = 'Teller';

这里的连接条件为`e1.emp_id < e2.emp_id`，表时每个柜员只会被安排与ID比自己高的柜员比赛。

*这里不能使用<>进行连接，因为这样对于每对比赛选手都会存在一个与之相反的选手对。*

### 5.5 连接条件和过滤条件

SQL对于防止条件的位置是很灵活的，因此需要仔细构建所需的查询。

假设现在有如下语句：

    SELECT a.account_id, a.product_cd, c.fed_id 
    FROM account a 
    INNER JOIN customer c
    ON a.cust_id = c.cust_id
    WHERE c.cust_type_cd = 'B';

返回数据：

account_id | product_cd | fed_id
-----|-----|-----
24 | CHK | 04-1111111
25 | BUS | 04-1111111
27 | BUS | 04-2222222
28 | CHK | 04-3333333
29 | SBL | 04-4444444

现在将过滤条件放在ON子句中：

    SELECT a.account_id, a.product_cd, c.fed_id 
    FROM account a 
    INNER JOIN customer c
    ON a.cust_id = c.cust_id 
    AND c.cust_type_cd = 'B';

或者将连接条件放在WHERE子句中，但FROM子句仍使用ANSI语法：

    SELECT a.account_id, a.product_cd, c.fed_id 
    FROM account a 
    INNER JOIN customer c
    WHERE a.cust_id = c.cust_id 
    AND c.cust_type_cd = 'B';

对于上述两个查询，MySQL服务器会产生相同的结果集。**尽管如此，还是应该在合适的位置放置合适的条件，即尽量使用ANSI语法。**

## 第六章 使用集合

>
关系型数据库通常处理的都是数据集合，本章介绍如何使用各种集合操作符来联合多个表。

### 6.1 集合理论基础

假设有以下两个集合：

- A = {1, 2, 3}
- B = {2, 3, 4}

**集合的并操作：**
A union B = {1, 2, 3, 4}，并操作将完全包含两个集合，但重复的元素只计算一次。

**集合的交操作：**
A intersect B = {2, 3}，交操作产生的数据集合只包括两个集合的重合数据，如果两个集合没有重合的数据，那么叫操作将产生空集。

**集合的差操作：**
A except B = {1}，差操作会在完整的A集合中去除与B集合重合的数据。如果两个集合没有重合部分，那么A对B的差操作结果为整个A集合。

还有一个常见操作，操作后的集合包括集合A和B中所有非重合区域的部分。这种数据集可以由以下两种方法产生：

- (A union B) except (A intersect B)
- (A except B) union (B except A)

### 6.2 集合理论实践

当使用集合理论处理实际的数据时，还需要了解数据集的结构。

以product表和customer表为例，两个表的定义如下：

    DESC product;

Field | Type | Null | Key | Default | Extra
-----|-----|-----|-----|-----|-----
product_cd | varchar(10) | NO | PRI | NULL | 
NAME | varchar(50) | NO |  | NULL | 
product_type_cd | varchar(10) | NO | MUL | NULL | 
date_offered | date | YES |  | NULL | 
date_retired | date | YES |  | NULL | 

    DESC customer;

Field | Type | Null | Key | Default | Extra
-----|-----|-----|-----|-----|-----
cust_id | int(10) unsigned | NO | PRI | NULL | auto_increment
fed_id | varchar(12) | NO |  | NULL | 
cust_type_cd | enum('I','B') | NO |  | NULL | 
address | varchar(30) | YES |  | NULL | 
city | varchar(20) | YES |  | NULL | 
state | varchar(20) | YES |  | NULL | 
postal_code | varchar(10) | YES |  | NULL | 

当对这两个表进行集合操作后，所产生的结果表中第一列应为`product.product_cd`和`customer.cust_id`列的组合，第二列为`product.name`和`customer.fed_id`的组合，以此类推。

其中一些列对是容易组合的（例如两列都是数字型），另一些列对则难以组合，例如数字列和字符串列。此外组合表的第6列和第7列数据只能来自于customer表的第6列和第7列，因为product表只包含5列。

由此看来，当对两个数据集合执行集合操作时，必须首先应用下面的规范：

- 两个数据集合必须具有相同数目的列
- 两个数据几种对应列的数据类型必须是一样的，或者服务器能够将其中一种转换为另一种

**在实践中使用这两条规则能够更容易地处理“重合数据”，即对于两张表中的数据行，如果每个列对都包含了同样的字符串、数字或日期等，那它们可以被视为重复行。**

在两条SELECT语句中可以使用集合操作符执行集合操作，例如：

    SELECT 1 num, 'abc' str
    UNION
    SELECT 9 num, 'xyz' str;

返回数据：

num | str
-----|-----
1 | abc
9 | xyz

每个独立的查询都会产生一个包含单个行的数据集合。集合操作符（UNION）告知数据库服务器连接两个集合的所有行。因此最终的集合包含两个两列的行。这种查询被称为符合查询，它将多个独立的查询组合到了一起。

### 6.3 集合操作符

SQL语言包含3个集合操作符以执行前述的各种集合操作。此外每个集合操作符可以有两种修饰符：一个表示包含重复项，另一个表示去除重复项（但不一定是所有重复项）。

#### 6.3.1 UNION操作符

UNION与UNION ALL操作符可以连接多个数据集，它们的区别在于UNION对连接后的集合排序并去除重复项，而UNION ALL保留重复项。使用UNION ALL得到的最终数据集的行数总是等于所要连接的各集合的行数之和，该操作是最易于执行的集合操作，因为服务器不需要检查重复的数据。

示例：从两个子类型客户表中产生完整的客户数据集合

    SELECT 'IND' type_cd, cust_id, CONCAT(fname, ' ',lname) NAME
    FROM individual
    UNION ALL
    SELECT 'BUS' type_cd, cust_id, NAME
    FROM business;

返回数据：

type_cd | cust_id | name
-----|-----|-----
IND | 1 | James Hadley
IND | 2 | Susan Tingley
IND | 3 | Frank Tucker
IND | 4 | John Hayward
IND | 5 | Charles Frasier
IND | 6 | John Spencer
IND | 7 | Margaret Young
IND | 8 | Louis Blake
IND | 9 | Richard Farley
BUS | 10 | Chilton Engineering
BUS | 11 | Northeast Cooling Inc.
BUS | 12 | Superior Auto Body
BUS | 13 | AAA Insurance Inc.

该查询返回了所有的13个客户，其中9行来自individual表，其他4行来自于business表。

下面的查询与上例类似，只不过连接了两次business表：

    SELECT 'IND' type_cd, cust_id, CONCAT(fname, ' ',lname) NAME
    FROM individual
    UNION ALL
    SELECT 'BUS' type_cd, cust_id, NAME
    FROM business
    UNION ALL
    SELECT 'BUS' type_cd, cust_id, NAME
    FROM business;

返回数据：

type_cd | cust_id | NAME
-----|-----|-----
IND | 1 | James Hadley
IND | 2 | Susan Tingley
IND | 3 | Frank Tucker
IND | 4 | John Hayward
IND | 5 | Charles Frasier
IND | 6 | John Spencer
IND | 7 | Margaret Young
IND | 8 | Louis Blake
IND | 9 | Richard Farley
BUS | 10 | Chilton Engineering
BUS | 11 | Northeast Cooling Inc.
BUS | 12 | Superior Auto Body
BUS | 13 | AAA Insurance Inc.
BUS | 10 | Chilton Engineering
BUS | 11 | Northeast Cooling Inc.
BUS | 12 | Superior Auto Body
BUS | 13 | AAA Insurance Inc.

从结果表中可以看出，来自于business表的4行数据被包含了两次。
*当然，在实际的复合查询中包含两个重复的查询并不常见。*

返回重复数据的复合查询例子：

    SELECT emp_id 
    FROM employee 
    WHERE assigned_branch_id = 2 
    AND title IN ('Teller', 'Head Teller')
    UNION ALL
    SELECT DISTINCT open_emp_id
    FROM account 
    WHERE open_branch_id = 2;

返回数据：

<table>
    <tr>
        <td>emp_id</td>
    </tr>   
    <tr>
        <td>10</td>
    </tr>
    <tr>
        <td>11</td>
    </tr>
    <tr>
        <td>12</td>
    </tr>
    <tr>
        <td>10</td>
    </tr>    
</table>

第一个查询获取分配到Woburn支行的所有柜员，第二个查询返回所有在Woburn支行开户的柜员（使用DISTINCT去重）。目前得到的结果集中有一行是重复的（ID为10的柜员），如果希望组合操作后的表排除重复行，那么需要使用UNION操作符代替UNION ALL：

    SELECT emp_id 
    FROM employee 
    WHERE assigned_branch_id = 2 
    AND title IN ('Teller', 'Head Teller')
    UNION
    SELECT DISTINCT open_emp_id
    FROM account 
    WHERE open_branch_id = 2;

<table>
    <tr>
        <td>emp_id</td>
    </tr>   
    <tr>
        <td>10</td>
    </tr>
    <tr>
        <td>11</td>
    </tr>
    <tr>
        <td>12</td>
    </tr>  
</table>

此时组合后的结果集中不再包含重复的数据行。

**从效率上说，UNION ALL 要比UNION快很多，所以，如果可以确认合并的两个结果集中不包含重复数据且不需要排序时的话，那么就使用UNION ALL。**

#### 6.3.2 INTERSECT操作符

>
MySQL无专门进行此操作的操作符，即MySQL还未实现该功能。

这里实现的思路为：对表进行UNION ALL操作，并将操作后的结果集作为临时表，然后再临时表中查找COUNT(*)大于1的项。

示例：

    SELECT emp_id, COUNT(*) 
    FROM
    (
    SELECT emp_id
    FROM employee 
    WHERE assigned_branch_id = 2
    AND title IN ('Teller', 'Head Teller')
    UNION ALL
    SELECT DISTINCT open_emp_id
    FROM account
    WHERE open_branch_id = 2
    ) tmp
    GROUP BY emp_id 
    HAVING COUNT(*) > 1;

返回数据：

emp_id | count(*)
-----|-----
10 | 2

下面分段进行解释：

    SELECT emp_id
    FROM employee 
    WHERE assigned_branch_id = 2
    AND title IN ('Teller', 'Head Teller')
    UNION ALL
    SELECT DISTINCT open_emp_id
    FROM account
    WHERE open_branch_id = 2

UNION ALL查询后的结果为：

<table>
    <tr>
        <td>emp_id</td>
    </tr>   
    <tr>
        <td>10</td>
    </tr>
    <tr>
        <td>11</td>
    </tr>
    <tr>
        <td>12</td>
    </tr>
    <tr>
        <td>10</td>
    </tr>    
</table>

直接对上述结果集进行查询（不进行分组并筛选）：

    SELECT emp_id, COUNT(*)
    FROM
    (
    SELECT emp_id
    FROM employee 
    WHERE assigned_branch_id = 2
    AND title IN ('Teller', 'Head Teller')
    UNION ALL
    SELECT DISTINCT open_emp_id
    FROM account
    WHERE open_branch_id = 2
    ) tmp

结果为：

emp_id | count(*)
-----|-----
10 | 4

对查询结果进行分组：

    SELECT emp_id, COUNT(*)
    FROM
    (
    SELECT emp_id
    FROM employee 
    WHERE assigned_branch_id = 2
    AND title IN ('Teller', 'Head Teller')
    UNION ALL
    SELECT DISTINCT open_emp_id
    FROM account
    WHERE open_branch_id = 2
    ) tmp
    GROUP BY emp_id 

结果为：

emp_id | count(*)
-----|-----
10 | 2
11 | 1
12 | 1

筛选出count(*)为2的记录：

    SELECT emp_id, COUNT(*)
    FROM
    (
    SELECT emp_id
    FROM employee 
    WHERE assigned_branch_id = 2
    AND title IN ('Teller', 'Head Teller')
    UNION ALL
    SELECT DISTINCT open_emp_id
    FROM account
    WHERE open_branch_id = 2
    ) tmp
    GROUP BY emp_id 
    HAVING COUNT(*) > 1;

即得到最终结果。

#### 6.3.3 except操作符

>
同样地，MySQL同样没有实现except操作符以执行集合的差操作。

这里实现的思路为：添加条件，使要查的表A的字段不在表B的对应字段中

示例：

    SELECT emp_id FROM employee
    WHERE assigned_branch_id = 2
    AND title IN ('Teller', 'Head Teller')
    AND emp_id NOT IN
    (SELECT open_emp_id FROM account);

返回数据：

<table>
    <tr>
        <td>emp_id</td>
    </tr>   
    <tr>
        <td>11</td>
    </tr>
    <tr>
        <td>12</td>
    </tr>    
</table>

为了避免NOT IN效率问题，也可以使用NOT EXISTS，示例：

    SELECT e.emp_id FROM employee e
    WHERE e.assigned_branch_id = 2
    AND e.title IN ('Teller', 'Head Teller')
    AND NOT EXISTS 
    (SELECT * FROM account a WHERE e.emp_id = a.open_emp_id);

或者可以使用左连接：

    SELECT e.emp_id FROM employee e
    LEFT JOIN account a ON e.emp_id = a.open_emp_id
    WHERE open_emp_id IS NULL
    AND e.assigned_branch_id = 2
    AND e.title IN ('Teller', 'Head Teller');

### 6.4 结合操作规则

#### 6.4.1 对复合查询结果排序

如果需要对复合查询的结果进行排序，那么可以在最后一次查询后面增加ORDER BY子句。当ORDER BY子句中指定要排序的列时，需要从复合查询的第一个查询中选择列名。
*通常情况下，复合查询中两个查询对应列的名字是相同的，但这并不是强制的。*

示例:

    SELECT emp_id, assigned_branch_id
    FROM employee
    WHERE title = 'Teller'
    UNION
    SELECT open_emp_id, open_branch_id
    FROM account
    WHERE product_cd = 'SAV'
    ORDER BY emp_id;

返回数据：

emp_id | assigned_branch_id
-----|-----
1 | 1
7 | 1
8 | 1
9 | 1
10 | 2
11 | 2
12 | 2
14 | 3
15 | 3
16 | 4
17 | 4
18 | 4

上例中两个查询指定的列名并不相同，瑞国再ORDER BY子句中指定的是来自于第二个查询的列名会发生报错。

#### 6.4.2 集合操作符优先级

如果复合查询包含两个以上使用不同集合操作符的查询，那么需要在复合语句中确定查询执行的次序，以获取想要的结果。

示例:

    SELECT cust_id 
    FROM account
    WHERE product_cd IN ('SAV', 'MM')
    UNION ALL
    SELECT a.cust_id
    FROM account a 
    INNER JOIN branch b
    ON a.open_branch_id = b.branch_id
    WHERE b.name = 'Woburn Branch'
    UNION 
    SELECT cust_id 
    FROM account 
    WHERE avail_balance BETWEEN 500 AND 2500;

返回数据：

<table>
    <tr>
        <td>cust_id</td>
    </tr>
    <tr>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
    </tr>
    <tr>
        <td>3</td>
    </tr>
    <tr>
        <td>4</td>
    </tr>
    <tr>
        <td>8</td>
    </tr>
    <tr>
        <td>9</td>
    </tr>
    <tr>
        <td>7</td>
    </tr>
    <tr>
        <td>11</td>
    </tr>
    <tr>
        <td>5</td>
    </tr>
</table>

第一个和第二个查询使用UNION ALL连接，第二个和第三个查询使用UNION连接。

尽管表面上看来UNION ALL和UNION操作符区别不大，但实际上它们还是有差别的。

示例：调换两个操作符的位置

    SELECT cust_id 
    FROM account
    WHERE product_cd IN ('SAV', 'MM')
    UNION 
    SELECT a.cust_id
    FROM account a 
    INNER JOIN branch b
    ON a.open_branch_id = b.branch_id
    WHERE b.name = 'Woburn Branch'
    UNION ALL
    SELECT cust_id 
    FROM account 
    WHERE avail_balance BETWEEN 500 AND 2500;

返回数据：

<table>
    <tr>
        <td>cust_id</td>
    </tr>
    <tr>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
    </tr>
    <tr>
        <td>3</td>
    </tr>
    <tr>
        <td>4</td>
    </tr>
    <tr>
        <td>8</td>
    </tr>
    <tr>
        <td>9</td>
    </tr>
    <tr>
        <td>7</td>
    </tr>
    <tr>
        <td>11</td>
    </tr>
    <tr>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
    </tr>
    <tr>
        <td>3</td>
    </tr>
    <tr>
        <td>3</td>
    </tr>
    <tr>
        <td>4</td>
    </tr>
    <tr>
        <td>4</td>
    </tr>
    <tr>
        <td>5</td>
    </tr>
    <tr>
        <td>9</td>
    </tr>
</table>

如上所述，用不同的集合操作符构建复合查询会产生不同的查询结果。
通常，复合查询包含3个或3个以上的查询语句，它们以自顶向下的顺序被解析和执行。但还要注意以下两点：

- 根据ANSI SQL标准，在调用集合时，INTERSECT操作符比其他操作符具有更高的优先级。
- 可以使用圆括号对多个查询进行封装，以明确它们的执行次序。

## 第七章 数据生成、转换和操作

>
本章主要处理对字符串、数字或临时数据的生成、转换和操作。由于SQL语言本身并不包含这些功能相关的命令，各数据库服务器必须使用内建函数处理数据生成、转换和操作，并且尽管SQL标准指定了一些函数，但有些厂商并没有遵从这些函数规范。
因此本章的目的在于介绍使用SQL语句的一些常用方式处理数据，并展示MS SQL Server、Oracle和MySQL的一些内建函数。

### 7.1 使用字符串数据

当使用字符串时可以使用下面的字符数据类型。

- CHAR：固定长度、不足的部分使用空格填充的字符串。MySQL的CHAR类型的长度为255，Oracle数据库允许2000各字符，而SQL Server的CHAR类型允许长度为8000。
- VARCHAR：变长字符串。MySQL允许VARCHAR列最多包含65536个字符，VARCHAR2（Oracle）允许包含4000个字符，SQL Server允许VARCHAR类型的最大长度为8000。
- TEXT（MySQL和SQL Server）或CLOB（Oracle）：容纳大长度的变长字符串（通常在上下文找那个指代文档）。MySQL具有多种TEXT类型，最大可以保存4GB大小的文档数据。SQL Server只具有一个最大长度为2GB的TEXT类型，而Oracle数据库包含的CLOB类型可以保存128TB的文档。

本章各示例使用下表：

    CREATE TABLE string_tbl
    (
        char_fld CHAR(30),
        vchar_fld VARCHAR(30),
        text_fld TEXT
    );

#### 7.1.1 生成字符串

生成字符串最简单的方式是用一对单引号将字符串包含起来。示例：

    INSERT INTO 
    string_tbl 
    VALUES('This is char data', 'This is varchar data', 'this is text data');

在向表中插入字符串数据时，需要保证其长度不超出字符列的最大长度（用户指定的或该数据类型内建的最大值），否则服务器将会抛出异常。但也可以通过对MySQL或SQL Server的配置，将其修改为直接阶段字符串后插入，并且不抛出任何异常。

示例：使用update语句向最大长度为30的vchar_fld列插入长度为46的字符串

    UPDATE string_tbl SET vchar_fld = 'This is a piece of extremely long varchar data';

发生报错：

    Error Code: 1406
    Data too long for column 'vchar_fld' at row 1

如果希望数据库引擎采用截断字符串的方式，可以将sql_mode得模式由默认的“strict”模式修改为“ANSI”模式。

示例：

先查看当前的sql_mode：

    SELECT @@session.sql_mode;

返回数据：

<table>
    <tr>
        <td>@@session.sql_mode</td>
    </tr>
    <tr>
        <td>STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION</td>
    </tr>
</table>

修改sql_mode：

    SET sql_mode = 'ANSI';

再次查看当前的sql_mode：

返回数据：

<table>
    <tr>
        <td>@@session.sql_mode</td>
    </tr>
    <tr>
        <td>REAL_AS_FLOAT,PIPES_AS_CONCAT,ANSI_QUOTES,IGNORE_SPACE,ONLY_FULL_GROUP_BY,ANSI</td>
    </tr>
</table>

再次运行UPDATE语句：

    UPDATE string_tbl SET vchar_fld = 'This is a piece of extremely long varchar data';

服务器产生信息：

    1 queries executed, 1 success, 0 errors, 1 warnings
    ...
    1 row(s) affected, 1 warning(s)
    ...
    Warning Code : 1265
    Data truncated for column 'vchar_fld' at row 1

此时vchar_fld的值被修改为“This is a piece of extremely l”。

**在使用VARCHAR列时避免字符串截断的最好方法是将列的上限设置为足够大，以处理可能存储在列中的最长字符串。由于服务器是在存储字符串时按需分配空间，因此不会因为将VARCHAR列的上限值设置得过大而浪费资源。**

**包含单引号：**

由于SQL中的字符串使用单引号分隔，因此对于本身包含单引号或撇号的字符串会发生报错。

示例：

    UPDATE string_tbl SET text_fld = 'This string doesn't work';

发生报错：

    Error Code: 1064
    You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 't work'' at line 1

为了使服务器忽略单词doesn't中的撇号，需要在字符串中加上转义符以便服务器能够将它视为普通字符。

示例：

    -- 大多数服务器都支持使用单引号作为转义符，
    UPDATE string_tbl SET text_fld = 'This string doesn''t work';
    -- 或者可以使用反斜杠作为转义符（Oracle、MySQL）
    UPDATE string_tbl SET text_fld = 'This string doesn\'t work';

在进行查询或提取字符串时，可以不使用任何特殊方式处理内嵌引号：

    SELECT text_fld FROM string_tbl;

返回数据：

<table>
    <tr>
        <td>text_fld</td>
    </tr>
    <tr>
        <td>This string doesn't work</td>
    </tr>
</table>

但是，当提取字符串并添加到另一个程序所读取的文件中时，或许需要为获取的字符串增加转义符。MySQL提供了内建函数QUOTE()，它用单引号将整个字符串包含起来，并未字符串本身的单引号或撇号增加转义符。

示例：

    SELECT QUOTE(text_fld) FROM string_tbl;

返回数据：

<table>
    <tr>
        <td>quote(text_fld)</td>
    </tr>
    <tr>
        <td>'This string doesn\'t work'</td>
    </tr>
</table>

**包含特殊字符：**

如果应用是国际化的，或许需要处理由键盘字符之外的字符所构成的字符串。SQL Server和MySQL提供了内建函数CHAR()可用于从ASCII字符集的255个字符中任意构建字符串（Oracle使用CHR()函数）。

示例：

    SELECT 'abcdefg', CHAR(97, 98, 99, 100, 101, 102, 103);

返回数据：

abcdefg | char(97, 98, 99, 100, 101, 102, 103)
-----|-----
abcdefg | abcdefg

使用CHAR()函数显示重音字符以及其他一些字符：

示例：

    SELECT CONCAT('danke sch', CHAR(148), 'n');

返回数据：

<table>
    <tr>
        <td>CONCAT('danke sch', CHAR(148), 'n')</td>
    </tr>
    <tr>
        <td>danke schön</td>
    </tr>
</table>

如果需要根据字符来查找对应的ASCII码，可以使用ASCII()函数，它接受一个字符并返回其符号：

    SELECT ASCII('ö');

返回数据：

<table>
    <tr>
        <td>ascii('ö')</td>
    </tr>
    <tr>
        <td>148</td>
    </tr>
</table>

#### 7.1.2 操作字符串

每种数据库服务器都包含许多用于操作字符串的内建函数。本节讨论其中两类字符串函数：返回数字的和返回字符串的。

重设string_tbl：

    DELETE FROM string_tbl;
    INSERT INTO string_tbl VALUES 
    ('This string is 28 characters', 'This string is 28 characters', 'This string is 28 characters');

**返回数字的字符串函数：**

最常用的返回数字的字符串函数是LENGTH()函数（SQL Server用LEN()），它返回字符串的字符数。

示例：

    SELECT LENGTH(char_fld) char_length, 
           LENGTH(vchar_fld) varchar_length, 
           LENGTH(text_fld) text_length 
    FROM string_tbl;

返回数据：

char_length | varchar_length | text_length
-----|-----|-----
28 | 28 | 28

这里VARCHAR和TEXT列的长度与预期一样。但是CHAR列的长度不应该是30吗？因为CHAR会向后自动补齐空格。**这是因为MySQL服务器在获取数据时会删除CHAR类型数据的尾端空格，因此无论存储字符串的列为何种类型，该字符串函数得到的结果都是相同的。**

除了确定字符串的长度，还可以查找字符串中子字符串的位置。

示例：

    SELECT POSITION('characters' IN vchar_fld) FROM string_tbl;

返回数据：

<table>
    <tr>
        <td>position('characters' IN vchar_fld)</td>
    </tr>
    <tr>
        <td>19</td>
    </tr>
</table>

如果找不到该子串，那么函数将返回0。

如果希望在字符串中的任意位置开始搜索，可以使用LOCATE()函数。该函数可以接受可选的3个参数。

示例：

    SELECT LOCATE('is', vchar_fld, 5) FROM string_tbl;

返回数据：

<table>
    <tr>
        <td>locate('is', vchar_fld, 5)</td>
    </tr>
    <tr>
        <td>13</td>
    </tr>
</table>

另一个接受字符串作为参数同时返回数字的函数是STRCMP()函数。该函数接受两个字符串作为参数，并返回下列结果之一：

- -1， 第一个字符串的排序位于第二个字符串之前
- 0，两个字符串相同
- 1，第一个字符串的排序位于第二个字符串之后

示例：

首先准备数据：

    DELETE FROM string_tbl;
    INSERT INTO string_tbl (vchar_fld) VALUES('abcd');
    INSERT INTO string_tbl (vchar_fld) VALUES('xyz');
    INSERT INTO string_tbl (vchar_fld) VALUES('QRSTUV');
    INSERT INTO string_tbl (vchar_fld) VALUES('qrstuv');
    INSERT INTO string_tbl (vchar_fld) VALUES('12345');

    SELECT vchar_fld FROM string_tbl ORDER BY 1;

返回数据：

<table>
    <tr>
        <td bgcolor=silver class='medium'>vchar_fld</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>12345</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>abcd</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>QRSTUV</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>qrstuv</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>xyz</td>
    </tr>
</table>

下面的查询对5个不同字符串进行6次比较：

    SELECT STRCMP('12345', '12345') 12345_12345,
           STRCMP('abcd', 'xyz') abcd_xyz,
           STRCMP('abcd', 'QRSTUV') abcd_QRSTUV,
           STRCMP('qrstuv', 'QRSTUV') qrstuv_QRSTUV,
           STRCMP('12345', 'xyz') 12345_xyz,
           STRCMP('xyz', 'qrstuv') xyz_qrstuv;

返回数据：

12345_12345 | abcd_xyz | abcd_QRSTUV | qrstuv_QRSTUV | 12345_xyz | xyz_qrstuv
-----|-----|-----|-----|-----|-----
0 | -1 | -1 | 0 | -1 | 1

第一个比较结果为0是显而易见的，因为二者相同。第4个比较结果也返回0，**这是因为该函数是大小写不敏感的。**其余比较结果取决于两个字符串的排序顺序。

除了使用STRCMP()函数，MySQL中还可以在SELECT语句中使用LIKE和REGEXP操作符来比较字符串。这些比较操作的结果为1（TRUE）和0（FALSE）。

示例：

    SELECT NAME, NAME LIKE '%ns' ends_in_ns FROM department;

返回结果：

name | ends_in_ns
-----|-----
Operations | 1
Loans | 1
Administration | 0

上例获取了所有部门名称以及一个表达式结果。其中，当部门名称以“ns”结尾时返回1；反之，返回0。

如果要执行更复杂的匹配时，可以使用REGEXP操作符。

示例：

    SELECT cust_id, 
           cust_type_cd, 
           fed_id, 
           fed_id REGEXP '.{3}-.{2}-.{4}' is_ss_no_format 
    FROM customer;

返回数据：

cust_id | cust_type_cd | fed_id | is_ss_no_format
-----|-----|-----|-----
1 | I | 111-11-1111 | 1
2 | I | 222-22-2222 | 1
3 | I | 333-33-3333 | 1
4 | I | 444-44-4444 | 1
5 | I | 555-55-5555 | 1
6 | I | 666-66-6666 | 1
7 | I | 777-77-7777 | 1
8 | I | 888-88-8888 | 1
9 | I | 999-99-9999 | 1
10 | B | 04-1111111 | 0
11 | B | 04-2222222 | 0
12 | B | 04-3333333 | 0
13 | B | 04-4444444 | 0

在上例查询中，如果fed_id的值于设定的模式匹配，则返回1。

**返回字符串的字符串函数：**
在某些情况下，需要修改育有的字符串，比如截取其中的一部分，或者向它添加额外的文本。每种数据库服务器都包含多个执行这些任务的函数。

重设string_tbl表：

    DELETE FROM string_tbl;
    INSERT INTO string_tbl (text_fld) VALUES ('This string was 29 characters');

使用CONCAT()函数为字符串增加额外字符：

    UPDATE string_tbl SET text_fld = CONCAT(text_fld, ', but now it is longer');

另一种使用CONCAT()的方式是根据独立的数据片段构建字符串，示例：

    SELECT emp_id, 
           CONCAT(fname, ' ', lname, ' has been a ', title, ' since', start_date) emp_narrative 
    FROM employee 
    WHERE title IN ('Teller', 'Head Teller');

返回数据：

emp_id | emp_narrative
-----|-----
6 | Helen Fleming has been a Head Teller since 2008-03-17
7 | Chris Tucker has been a Teller since 2008-09-15
8 | Sarah Parker has been a Teller since 2006-12-02
9 | Jane Grossman has been a Teller since 2006-05-03
10 | Paula Roberts has been a Head Teller since 2006-07-27
11 | Thomas Ziegler has been a Teller since 2004-10-23
12 | Samantha Jameson has been a Teller since 2007-01-08
13 | John Blake has been a Head Teller since 2004-05-11
14 | Cindy Mason has been a Teller since 2006-08-09
15 | Frank Portman has been a Teller since 2007-04-01
16 | Theresa Markham has been a Head Teller since 2005-03-15
17 | Beth Fowler has been a Teller since 2006-06-29
18 | Rick Tulman has been a Teller since 2006-12-12

MySQL还提供了INSERT()函数，它接受4个参数：原始字符串、字符串操作的开始位置、需要替换的字符数、以及替换字符串。根据第三个参数值，函数可以选择插入或替换原始字符串中的字符。如果该参数值是0，那么替换字符串将会被插入其中，并且剩余的字符将会向右移动。

示例：

    SELECT INSERT('goodbye world', 9, 0, 'cruel ') string;

返回数据：

<table>
    <tr>
        <td>string</td>
    </tr>
    <tr>
        <td>goodbye cruel world</td>
    </tr>
</table>

上例中，所有从第9位（w）开始的字符都向右移动，以容纳插入的字符串‘cruel’。如果第三个参数大于0，那么相应数目的字符将会被替换字符串所取代。

示例：

    SELECT INSERT('goodbye world', 9, 2, 'cruel ') string;

返回数据：

<table>
    <tr>
        <td>string</td>
    </tr>
    <tr>
        <td>goodbye cruel rld</td>
    </tr>
</table>

其中第9位（w）和第10位（o）被字符串‘cruel’替换。

除了向字符串中插入字符外，还可以从字符串中提取子字符串。MySQL使用substring()函数，它从指定位置开始提取指定数目的字符。

示例：从字符串的第9个位置提取5个字符

    SELECT SUBSTRING('goodbye cruel world', 9, 5);

返回数据：

<table>
    <tr>
        <td>substring('goodbye cruel world', 9, 5)</td>
    </tr>
    <tr>
        <td>cruel</td>
    </tr>
</table>

### 7.2 使用数值数据

与字符串数据以及临时数据不同，数值型数据的生成十分简单，可以通过输入一个数字、从另一个是指列提取或者通过计算产生。所有的算数操作符（+、-、*、/）都可以用于执行计算，并且可以使用括号改变优先级。

示例：

    SELECT (37 * 59) / (78 - (8 * 6));

返回数据：

<table>
    <tr>
        <td>(37 * 59) / (78 - (8 * 6))</td>
    </tr>
    <tr>
        <td>72.7667</td>
    </tr>
</table>

如果数值型数据的精度大于所在列的长度，那么在其存储时可能会发生取整操作。例如9.96在被存放到定义位FLOAT(3, 1)的列时将会被取整为10.0。

#### 7.2.1 执行算术函数

大多数内建的处理数值的函数都用于特定的算术目的，比如求出某数的平方根。

常用的数值函数：


函数名 | 描述
-----|-----
ACOS(x) | 反余弦函数
ASIN(x) | 反正弦函数
ATAN(x) | 反正切函数
COS(x) | 余弦函数
COT(x) | 余切函数
EXP(x) | 以e为底的指数函数
LN(x) | 以e为底的对数函数
SIN(x) | 正弦函数
SQRT(x) | 开平方根函数
TAN(x) | 正切函数

求模操作在MySQL中通过MOD()函数实现，示例：

    SELECT MOD(10, 4);

返回数据：

<table>
    <tr>
        <td>mod(10, 4)</td>
    </tr>
    <tr>
        <td>2</td>
    </tr>
</table>

MOD()函数主要用于整形参数，但MySQL还可以用它来处理实数，示例：

    SELECT MOD(22.75, 5);

返回数据：

<table>
    <tr>
        <td>mod(22.75, 5))</td>
    </tr>
    <tr>
        <td>2.75</td>
    </tr>
</table>

另一种接受两个数值参数的函数是POW()，他返回两个参数的幂计算，示例：

    SELECT POW(2,8);

返回数据：

<table>
    <tr>
        <td>pow(2,8)</td>
    </tr>
    <tr>
        <td>256</td>
    </tr>
</table>

*POW()函数可以作为一种获取某段内存确切字节数的简易方法。*

    SELECT POW(2, 10) kilobyte, 
           POW(2, 20) megabyte, 
           POW(2, 30) gigabyte, 
           POW(2, 40) terabyte;

返回数据：

kilobyte | megabyte | gigabyte | terabyte
-----|-----|-----|-----
1024 | 1048576 | 1073741824 | 1099511627776

#### 7.2.2 控制数字精度

当处理浮点数时，或许不需要总是以数字的全部精度进行计算及显示。

有4个函数可以用于限制浮点数的精度：

- CEIL()
- FLOOR()
- ROUND()
- TRUNCATE()

CEIL()函数和FLOOR()函数用于向上或向下截取整型数字，示例：

    SELECT CEIL(72.445), FLOOR(72.445);

返回数据：

ceil(72.445) | floor(72.445)
-----|-----
73 | 72

CEIL()函数向上进位，无论该数字的小数部分多么小；而FLOOR()函数则将向下进位，无论这个小数部分有多大。示例：

    SELECT CEIL(72.0000001), FLOOR(72.9999999);

ceil(72.0000001) | floor(72.9999999)
-----|-----
73 | 72

ROUND()函数用于向上或向下取整，其结果取决于两个整数之间的中间点（四舍五入），对于小数部分大于等于0.5的数值将向上取整，反之向下取整。示例：

    SELECT ROUND(72.49999), ROUND(72.5), ROUND(72.50001);

返回数据：

round(72.49999) | round(72.5) | round(72.50001)
-----|-----|-----
72 | 73 | 73

当需要按照特定位数保留小数部分，而不是进行取整时，ROUND()函数还提供了一个可选的第二个参数已制定在小数点右侧保留多少位。

示例：

    SELECT ROUND(72.0909, 1), ROUND(72.0909, 2), ROUND(72.0909, 3);

返回数据：

round(72.0909, 1) | round(72.0909, 2) | round(72.0909, 3)
-----|-----|-----
72.1 | 72.09 | 72.091

与ROUND()函数类似，TRUNCATE()函数也允许接受第二个可选参数，以指定小数点右侧要保留的位数，**但是该函数只是简单地去掉不需要的小数位，而不进行四舍五入。**

示例：

    SELECT TRUNCATE(72.0909, 1), TRUNCATE(72.0909, 2), TRUNCATE(72.0909, 3);

返回数据：

truncate(72.0909, 1) | TRUNCATE(72.0909, 2) | TRUNCATE(72.0909, 3)
-----|-----|-----
72.0 | 72.09 | 72.090

TRUNCATE()和ROUND()函数都可以为第二个参数指定一个负数，表示小数点左侧需要被截取或取整多少位。

示例：

    SELECT ROUND(17, -1), TRUNCATE(17, -1);

返回数据：

round(17, -1) | truncate(17, -1)
-----|-----
20 | 10

#### 7.2.3 处理有符号数

如果所用数字列允许存储负数，那么可能会用到下面几种数值函数。

示例：

    SELECT account_id, 
           avail_balance, 
           SIGN(avail_balance), 
           ABS(avail_balance) 
    FROM account 
    WHERE account_id <= 20;

返回数据：

account_id | avail_balance | sign(avail_balance) | abs(avail_balance)
-----|-----|-----|-----
1 | 1057.75 | 1 | 1057.75
2 | 500.00 | 1 | 500.00
3 | 3000.00 | 1 | 3000.00
4 | 2258.02 | 1 | 2258.02
5 | 200.00 | 1 | 200.00
7 | 1057.75 | 1 | 1057.75
8 | 2212.50 | 1 | 2212.50
10 | 534.12 | 1 | 534.12
11 | 767.77 | 1 | 767.77
12 | 5487.09 | 1 | 5487.09
13 | 2237.97 | 1 | 2237.97
14 | 122.37 | 1 | 122.37
15 | 10000.00 | 1 | 10000.00
17 | 5000.00 | 1 | 5000.00
18 | 3487.19 | 1 | 3487.19
19 | 387.99 | 1 | 387.99

这里第二列使用了SIGN()函数，当帐户余额为负数时返回-1，在账户余额为0时返回0，在账户余额为正数时返回1.第三列使用了ABS()函数，返回帐户余额的绝对值。

### 7.3 使用时间数据

时间数据的生成和操作最为复杂，造成其复杂性的部分原因在于存在众多记录日期和时间的方式。

例如：

- Wednesday, September 17, 2008
- 9/17/2008 2:14:56 P.M. EST
- 9/17/2008 19:14:56 GMT
- 2612008 (Julian format)
- Star date[-4] 85712.03 14:14:56 (Star Trek format)

#### 7.3.1 处理时区

格林威治标准时间或GMT，其他所有时区都可以使用与GMT所差小时数来表述。例如美国东部时区，即东部标准时间可以用GMT-5:00来表示，或者说比GMT早5个小时。

协调世界时或UTC，它基于原子时钟，被称为世界时。MySQL中可以使用UTC_TIMESTAMP()函数返回当前的UTC时间戳。

MySQL提供两个不同的时区设置：全局时区和会话时区，后者可能对每个登录用户都是不同的。

    SELECT @@global.time_zone, @@session.time_zone;

返回数据：

@@global.time_zone | @@session.time_zone
-----|-----
SYSTEM | SYSTEM

结果为SYSTEM代表服务器根据数据库所在地使用相应的时区设置。

在进行设置前需要导入时区信息，timezone_posix.sql。

具体方法：

    mysql -u root -p 
    ...
    use mysql;
    source (path)\timezone_posix.sql

在添加了时区信息后，就可以设置时区信息了。

    SET time_zone = 'Europe/Zurich';

再次查询时区得到数据：

@@global.time_zone | @@session.time_zone
-----|-----
SYSTEM | Europe/Zurich

此时在会话中显示的所有日期都复合苏黎世时间。

#### 7.3.2 生成时间数据

可以使用下面任意一种方法产生时间数据：

- 从以有的DATE、DATETIME或TIME中复制数据
- 执行返回DATE、DATETIME或TIME型数据的内建函数
- 构建可以被服务器识别的代表日期的字符串

为了使用最后一种方法，我们必须首先理解格式化日期的各种组件。

**表示日期数据的字符串：**

数据格式组件：

组件 | 定义 | 范围
-----|-----|-----
YYYY | 年份，包括世纪 | 1000 ~ 9999
MM | 月份 | 01(January) ~ 12(December)
DD | 日 | 01 ~ 31
HH | 小时 | 00 ~ 23
HHH | 小时（过去的） | -838 ~ 838
MI | 分钟 | 00 ~ 59
SS | 秒 | 00 ~ 59 

为了构建服务器可以识别为DATE、DATETIME或TIME类型的字符串，需要按照顺序整合各种日期部件：

类型 | 默认格式 | 允许的值
-----|-----|-----
DATE | YYYY-MM-DD | 1000-01-01 ~ 9999-12-31
DATETIME | YYYY-MM-DD HH:MI:SS | 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59
TIMESTAMP | YYYY-MM-DD HH:MI:SS | 1970-01-01 00:00:00 ~ 2037-12-31 23:59:59
TIME |HHH:MI:SS | -838:59:59 ~ 838:59:59

示例，为了向DATETIME列中添加一条2008年9月17日下午15:00点的时间数据，需要使用下面的字符串：

    '2008-09-17 15:30:00'

当该字符串用于DATETIME列时，服务器将会试着通过将字符串分解为DATETIME格式所默认的6个组件的方式，对其进行转换。

**字符串到日期的转换：**

如果服务器并没有期望DATETIME类型的值，或者使用了非默认格式来表示DATETIME，那么就需要告知服务器将字符串转换为DATETIME，示例：

    SELECT CAST('2008-09-17 15:30:00' AS DATETIME);

返回数据：

<table>
    <tr>
        <td>cast('2008-09-17 15:30:00' AS DATETIME)</td>
    </tr>
    <tr>
        <td>2008-09-17 15:30:00</td>
    </tr>
</table>

对于DATE和TIME类型也可以使用同样的逻辑：

    SELECT CAST('2008-09-17' AS DATE) date_field, CAST('15:30:00' AS TIME) time_field;
返回数据：

date_field | time_field
-----|-----
2008-09-17 | 15:30:00

无论使用何种转换，在字符串转换为时间值时，必须按照规定次序提供所有的日期部件。某些服务器对日期格式要求十分严格，而MySQL服务器对于各部件至阿健的分隔符的要求比较宽松，例如MySQL可以接受下面各种表示2018年9月17日下午15：30分的字符串：

- '2008-09-17 15:30:00' 
- '2008/09/17 15:30:00'
- '2008,09,17,15,30,00'
- '20080917153000'

**产生日期的函数：** 
如果需要根据字符串产生时间数据，但所提供的字符串不是CAST()函数所接受的格式，那么可以使用内建函数将字符串格式化为日期字符串。比如MySQL的str_to_date函数。

示例：

    UPDATE ...
    SET xxx_date = STR_TO_DATE('September 17, 2008', '%M %d, %Y');
    WHERE ...

STR_TO_DATE()函数的第二个参数指明了带转换字符串的日期格式。该函数可以识别30多种格式部件。

日期格式常用部件：

- %a：星期几的简写，比如Sun、Mon
- %b：月名称的简写，比如Jan、Feb
- %c：月份的数字形式（0..12）
- %d：日在月中的次序（00..31）
- %f：毫秒数（000000..999999）
- %H：24时格式中的小时（00..23）
- %h：12时格式中的小时（01..12）
- %i：小时中的分钟（00..59）
- %j：一年中天的次序（001..366）
- %M：完整的月名称（January..December）
- %m：月份的数字表示
- %p：AM或PM
- %s：秒数（00..59）
- %W：完整的星期名（Sunday..Saturday）
- %w：天在星期中的次序（0 = 周日..6 = 周六）
- %Y：4位数字的年份

STR_TO_DATE()将根据格式字符串的内容返回DATETIME、DATE或TIME类型值。例如，如果格式字符质保函%H、%i或%s，那么返回TIME值。

如果需要返回当前日期/时间，可以直接通过内建函数获取系统时钟并返回当前的日期或时间字符串：

    SELECT CURRENT_DATE(), CURRENT_TIME(), CURRENT_TIMESTAMP();

返回数据：

current_Date() | current_time() | current_timestamp()
-----|-----|-----
2018-03-18 | 09:24:39 | 2018-03-18 09:24:39

#### 7.3.3 操作时间数据

许多内建的时间函数接受一个日期型数值作为参数，然后返回另一个日期。例如MySQL的DATE_ADD()函数可以为指定日期增加任意一段时间间隔并产生另一个日期。

示例：

    SELECT DATE_ADD(CURRENT_DATE(), INTERVAL 5 DAY);

返回数据：

<table>
    <tr>
        <td>date_add(CURRENT_DATE(), INTERVAL 5 DAY)</td>
    </tr>
    <tr>
        <td>2018-03-23</td>
    </tr>
</table>

其中第二个参数包含了3个元素：

- INTERVAL关键字
- 所需要增加的数量
- 时间间隔的类型

常用的时间间隔类型：

间隔名称 | 描述
-----|-----
SECOND | 秒数
MINUTE | 分钟数
HOUR | 小时数
DAY | 天数
MONTH | 月份
YEAR | 年份
MINUTE_SECOND | 分钟数和描述，中间用“:”隔开
HOUR_SECOND |  小时数、分钟数和秒数，中间用“:”隔开
YEAR_MONTH | 年份和月份，中间用“-”隔开

示例，例如当得知id为9999的交易实际发生的事件比transaction表中记录的时间要晚3小时27分钟11秒，那么可以使用以下方法进行修正：

    UPDATE transaction
    SET txn_date = DATE_ADD(txn_date, INTERVAL '3:27:11' HOUR_SECOND)
    WHERE txn_id = 9999;

LAST_DAY()函数可以求得当月得最后一天。示例：

    SELECT LAST_DAY('2018-3-18');

返回数据：

<table>
    <tr>
        <td>last_day('2018-3-18')</td>
    </tr>
    <tr>
        <td>2018-03-31</td>
    </tr>
</table>

无论所提供得参数是DATE型还是DATETIME型，该函数总是返回一个DATE值。

CONVERT_TZ()函数能够将某个时区得DATETIME转换为另一个时区对应得时间。

示例：将当前时间转换为UTC时间

    SELECT CURRENT_TIMESTAMP() current, 
           CONVERT_TZ(CURRENT_TIMESTAMP(), 'US/Eastern', 'UTC') current_utc;

返回数据：

current | current_utc
-----|-----
2018-03-18 21:12:26 | 2018-03-19 01:12:26

在收到与数据库所在地不同得另一个时区得日期值时，可以利用该函数进行转换。

**返回字符串得时间函数：**

返回字符串的时间类函数大多数用于提取日期或时间的一部分。

DAYNAME()函数可以确定某一日期是星期几：

    SELECT DAYNAME(CURRENT_DATE());

返回数据：

<table>
    <tr>
        <td>dayname(CURRENT_DATE())</td>
    </tr>
    <tr>
        <td>Sunday</td>
    </tr>
</table>

EXTRACT()函数也可以用于在DATETIME中提取数据（*SQL:2003标准，建议使用*）。

示例：

    SELECT EXTRACT(DAY FROM CURRENT_DATE());

<table>
    <tr>
        <td>extract(DAY FROM CURRENT_DATE())</td>
    </tr>
    <tr>
        <td>18</td>
    </tr>
</table>

**返回数字的时间函数：**

另一种常见的操作日期的行为是接受两个日期，并求出它们之间的时间间隔。

MySQL提供了DATEDIFF()函数，他返回两个日期之间的天数，示例：

    SELECT DATEDIFF('2009-09-03 23:59:59', '2009-06-24 00:00:01');

返回数据：

<table>
    <tr>
        <td>datediff('2009-09-03 23:59:59', '2009-06-24 00:00:01')</td>
    </tr>
    <tr>
        <td>71</td>
    </tr>
</table>

该函数将完全忽略TIME值。并且如果交换数据，那么函数将返回负值。

### 7.4 转换函数

使用CAST()函数使必须提供一个作为关键字的值或表达式，以及所需要转换的类型。

示例：

    SELECT CAST('123456' AS SIGNED INTEGER);

返回数据：

<table>
    <tr>
        <td>cast('123456' AS SIGNED INTEGER)</td>
    </tr>
    <tr>
        <td>123456</td>
    </tr>
</table>

**当函数CAST()在将字符串转换为数字时，首先会从左向右试着对整个字符串进行转换，如果期间在字符串中遇到非数字的字符，那么转换将中止并且不返回错误。**

示例：

    SELECT CAST('1234A56' AS SIGNED INTEGER);

返回数据：

<table>
    <tr>
        <td>cast('1234A56' AS SIGNED INTEGER)</td>
    </tr>
    <tr>
        <td>1234</td>
    </tr>
</table>

并给出警告提示字符串并没有被完全转换：

    Warning Code : 1292
    Truncated incorrect INTEGER value: '1234A56'

总的来说，如果要将字符串转换为DATE、TIME或DATETIME值，酒必须严格遵守每种类型的默认格式。如果待转换的日期字符串不是默认格式，那么首先需要使用其他函数（例如STR_TO_DATE()函数）将之重新排列。

## 第八章 分组与聚集

>
对于数据库用户而言，通常数据的存储粒度总是越低越好，但这并不意味着要用户必须按数据在数据库中的存储方式对其进行处理。本章聚焦于如何对数据进行分组与聚集，以使用户在更高的粒度层次上与数据进行交互。

### 8.1 分组概念

有时需要在数据中找到变化趋势，这就需要数据库服务器在产生所需要的结果集之前对数据进行一些加工。

示例：查找每个柜员创建了多少个账户

    SELECT open_emp_id FROM account;

返回数据：

<table>
    <tr>
        <td bgcolor=silver class='medium'>open_emp_id</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>1</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>10</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>10</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>10</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>10</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>10</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>10</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>10</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>13</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>13</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>13</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>16</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>16</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>16</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>16</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>16</td>
    </tr>
    <tr>
        <td class='normal' valign='top'>16</td>
    </tr>
</table>

该表共24条记录，很容易得出它们是被4个不同的职员创建的。但是，当表内有成千上万个账户，那么此查询的结果将十分复杂而难以查看。

这时可以通过GROUP BY子句进行分组：

    SELECT open_emp_id FROM account GROUP BY open_emp_id;

返回数据：

<table>
    <tr>
        <td>open_emp_id</td>
    </tr>
    <tr>
        <td>1</td>
    </tr>
    <tr>
        <td>10</td>
    </tr>
    <tr>
        <td>13</td>
    </tr>
    <tr>
        <td>16</td>
    </tr>
</table>

结果集中每行对应了一个open_emp_id列的都离职，因此一共只有4行，而不是全部24行。如果想要查看每个柜员所创建的账户数，那么可以在SELECT子句中使用聚集函数，已统计每个分组的行数目：

    SELECT open_emp_id, COUNT(*) how_many FROM account GROUP BY open_emp_id;

返回数据：

open_emp_id | how_many
-----|-----
1 | 8
10 | 7
13 | 3
16 | 6

其中聚集函数count()计算每个分组的行数，星号表示对所有列进行技术。

当对数据分组时，或许还需要在结果集中过滤掉不想要的数据，并且过滤条件是针对分组数据而不是原始数据。这时，需要使用HAVING子句。

示例：

    SELECT open_emp_id, 
           COUNT(*) how_many 
    FROM account 
    GROUP BY open_emp_id 
    HAVING COUNT(*) > 4;

返回数据：

open_emp_id | how_many
----- | -----
1 | 8
10 | 7
16 | 6

### 8.2 聚集函数

聚集函数对某个分组的所有行执行特定操作。尽管每种数据库服务器都具有独立的聚集函数，但一些通用的聚集函数在所有主流服务器上都得到了实现。

- MAX()：返回集合中的最大值
- MIN()：返回集合中的最小值
- AVG()：返回集合中的平均值
- SUM()：返回集合中所有值的和
- COUNT()：返回集合中值的个数

示例：

    SELECT MAX(avail_balance) max_balance, 
           MIN(avail_balance) min_balance, 
           AVG(avail_balance) avg_balance, 
           SUM(avail_balance) tot_balance, 
           COUNT(*) num_accounts 
    FROM account 
    WHERE product_cd = 'CHK';

返回数据：

max_balance | min_balance | avg_balance | tot_balance | num_accounts
-----|-----|-----|-----|-----
38552.05 | 122.37 | 7300.800985 | 73008.01 | 10

#### 8.2.1 隐式或显式分组

上例中，查询返回的每个值都是由聚集函数产生的，这些聚集函数作用于使用过滤条件`product_cd = 'CHK'`指定分组上的所有行。这里没有使用GROUP BY子句，因此它是一个隐式分组（**即包含查询返回的所有行**）。

假设现在需要扩展前一个查询，使之为每种产品类型执行同样的5种聚集函数，而不只针对核算账户。即需要在列举5个聚集函数结果的同时提取product_cd列：

    SELECT product_cd,
           MAX(avail_balance) max_balance, 
           MIN(avail_balance) min_balance, 
           AVG(avail_balance) avg_balance, 
           SUM(avail_balance) tot_balance, 
           COUNT(*) num_accounts 
    FROM account;

返回数据：

product_cd | max_balance | min_balance | avg_balance | tot_balance | num_accounts
-----|-----|-----|-----|-----|-----
CHK | 50000.00 | 0.00 | 7114.769138 | 170754.46 | 24

这里由于没有显式的指明分组，导致全部数据被直接隐式的分为了一组。因此需要为这个查询增加一个GROUP BY子句，以指定聚集函数所作用的分组：

    SELECT product_cd,
           MAX(avail_balance) max_balance, 
           MIN(avail_balance) min_balance, 
           AVG(avail_balance) avg_balance, 
           SUM(avail_balance) tot_balance, 
           COUNT(*) num_accounts 
    FROM account
    GROUP BY product_cd;

返回数据：

product_cd | max_balance | min_balance | avg_balance | tot_balance | num_accounts
-----|-----|-----|-----|-----|-----
BUS | 9345.55 | 0.00 | 4672.774902 | 9345.55 | 2
CD | 10000.00 | 1500.00 | 4875.000000 | 19500.00 | 4
CHK | 38552.05 | 122.37 | 7300.800985 | 73008.01 | 10
MM | 9345.55 | 2212.50 | 5681.713216 | 17045.14 | 3
SAV | 767.77 | 200.00 | 463.940002 | 1855.76 | 4
SBL | 50000.00 | 50000.00 | 50000.000000 | 50000.00 | 1

通过GROUP BY子句，服务器将为在product_cd列上具有同样值得行进行分组，然后再这6个分组上应用5种聚集函数。

#### 8.2.2 对独立值计数

当使用COUNT()函数确定每个分组成员数目时，可以选择是对分组中所有成员技术还是只技术某个列的不同值。

示例：查询每个账户开户的相应雇员的id

    SELECT account_id, open_emp_id FROM account ORDER BY open_emp_id;

返回数据：

account_id | open_emp_id
-----|-----
10 | 1
11 | 1
12 | 1
14 | 1
15 | 1
21 | 1
22 | 1
23 | 1
1 | 10
2 | 10
3 | 10
4 | 10
5 | 10
17 | 10
27 | 10
7 | 13
8 | 13
29 | 13
13 | 16
18 | 16
19 | 16
24 | 16
25 | 16
28 | 16

如表所示，这些账户的开户者只包括4个不同的雇员（1、10、13、16）。

假设需要创建查询以获取完成个开户的雇员数，而不是通过观察结果来手动计数，那么将COUNT()函数作用到open_emp_id列会得到以下结果：

    SELECT COUNT(open_emp_id) FROM account;

返回结果：

<table>
    <tr>
        <td>count(open_emp_id)</td>
    </tr>
    <tr>
        <td>24</td>
    </tr>
</table>

在上述情况下，指定open_emp_id列作为计数列所产生的结果与指定COUNT(*)相同。

如果希望对不同值进行计数，而不是统计所有行，则需要使用DISTINCT：

    SELECT COUNT(DISTINCT open_emp_id) FROM account;

返回结果：

<table>
    <tr>
        <td>count(distinct open_emp_id)</td>
    </tr>
    <tr>
        <td>4</td>
    </tr>
</table>

通过指定DISTINCT，COUNT()函数检查分组每个成员的特定列的值，并去除发生重复的行，而不是简单地对分组中所有行进行计数。

#### 8.2.3 使用表达式

除了使用列作为聚集函数的参数外，还可以创建表达式作为参数。

示例：

    SELECT MAX(pending_balance - avail_balance) max_uncleared FROM account;

返回数据：

<table>
    <tr>
        <td>max(pending_balance - avail_balance)</td>
    </tr>
    <tr>
        <td>660.00</td>
    </tr>
</table>

实际上用于聚集函数的参数表达式可以根据需要任意增加复杂度，只需要保证最后返回一个数字、字符串或日期即可。

#### 8.2.4 如何处理NULL值

**当执行聚集函数或其他数值计算时，应当首先考虑NULL值是否可能会影响计算结果。**

本节使用下述表和数据：

    CREATE TABLE number_tbl
    (
        val SMALLINT
    );
    INSERT INTO number_tbl VALUES (1);
    INSERT INTO number_tbl VALUES (3);
    INSERT INTO number_tbl VALUES (5);

针对该表执行5个聚集函数：

    SELECT COUNT(*) num_rows, 
           COUNT(val) num_vals, 
           SUM(val) total, 
           MAX(val) max_val, 
           AVG(val) avg_val 
    FROM number_tbl;

返回数据：

num_rows | num_vals | total | max_val | avg_val
-----|-----|-----|-----|-----
3 | 3 | 9 | 5 | 3.0000

现在向表插入一行NULL值并再次查询：

    INSERT INTO number_tbl VALUES (NULL);
    SELECT COUNT(*) num_rows, 
           COUNT(val) num_vals, 
           SUM(val) total, 
           MAX(val) max_val, 
           AVG(val) avg_val 
    FROM number_tbl;

返回数据：

num_rows | num_vals | total | max_val | avg_val
-----|-----|-----|-----|-----
4 | 3 | 9 | 5 | 3.0000

向表中增加一个NULL后，SUM()、MAX()、AVG()函数的返回值没有发生变化，这表明它们忽略了任何遇到的NULL值。COUNT(\*)函数返回4，这时由于number_tbl表当前包含4行，而COUNT(val)函数仍旧返回3。二者的区别在于COUNT(\*)对行的数目计数，而COUNT(val)对val列所包含的值的数目进行计数并忽略所有的NULL值。

### 8.3 产生分组

通常人们很少对原始数据感兴趣，而更希望对原始数据进行加工以便适应数据分析的需要。为了解决这个需求，需要请求数据库服务器根据列（一个或多个）或表达式对数据行进行分组。

#### 8.3.1 对单列的分组

对单列的分组是最简单的同时也是最常用的。

示例：

    SELECT product_cd, SUM(avail_balance) prod_balance FROM account GROUP BY product_cd;

返回数据：

product_cd | prod_balance
-----|-----
BUS | 9345.55
CD | 19500.00
CHK | 73008.01
MM | 17045.14
SAV | 1855.76
SBL | 50000.00

该查询产生6个分组，分别对应6中产品，然后对每个分组成员的可用余额进行合计。

#### 8.3.2 对多列的分组

在某些情况下，需要根据多列产生分组。

示例：根据产品和开户支行进行统计

    SELECT product_cd, 
           open_branch_id, 
           SUM(avail_balance) prod_balance 
    FROM account 
    GROUP BY product_cd, open_branch_id;

返回数据：

product_cd | open_branch_id | prod_balance
-----|-----|-----
BUS | 2 | 9345.55
BUS | 4 | 0.00
CD | 1 | 11500.00
CD | 2 | 8000.00
CHK | 1 | 782.16
CHK | 2 | 3315.77
CHK | 3 | 1057.75
CHK | 4 | 67852.33
MM | 1 | 14832.64
MM | 3 | 2212.50
SAV | 1 | 767.77
SAV | 2 | 700.00
SAV | 4 | 387.99
SBL | 3 | 50000.00

该查询共产生14个分组，分别对应account表中每种产品与支行的组合。因为open_branch_id是从表中获取而不能由聚集函数产生，所以必须同时在SELECT子句和GROUP BY子句中增加此列。

#### 8.3.3 利用表达式分组

除了根据列进行分组，还可以根据表达式产生的值进行分组。

示例：根据入职年份对雇员进行分组

    SELECT EXTRACT(YEAR FROM start_date) YEAR, 
           COUNT(*) how_many 
    FROM employee 
    GROUP BY 1;

返回数据：

year | how_many
-----|-----
2003 | 1
2004 | 2
2005 | 3
2006 | 8
2007 | 2
2008 | 2

该查询的表达式很简单，只是使用EXTRACT()函数获取并返回日期中的年份，然后据此对employee表的数据进行分组。

#### 8.3.4 产生合计数

现在假设需要在为每种产品/支行组合计算合计余额的同时，还需要为每种产品单独计算合计数，这时需要使用WITH ROLLUP选项。

示例：

    SELECT product_cd, 
           open_branch_id, 
           SUM(avail_balance) tot_balance 
    FROM account 
    GROUP BY 1, 2 WITH ROLLUP;

返回数据：

product_cd | open_branch_id | tot_balance
-----|-----|-----
BUS | 2 | 9345.55
BUS | 4 | 0.00
BUS | NULL | 9345.55
CD | 1 | 11500.00
CD | 2 | 8000.00
CD | NULL | 19500.00
CHK | 1 | 782.16
CHK | 2 | 3315.77
CHK | 3 | 1057.75
CHK | 4 | 67852.33
CHK | NULL | 73008.01
MM | 1 | 14832.64
MM | 3 | 2212.50
MM | NULL | 17045.14
SAV | 1 | 767.77
SAV | 2 | 700.00
SAV | 4 | 387.99
SAV | NULL | 1855.76
SBL | 3 | 50000.00
SBL | NULL | 50000.00
NULL | NULL | 170754.46

现在结果集中由7个额外的行，分别对应6个独立的产品以及总合计数。对于6种产品的合计行，其中open_branch_id列为NULL，因为这些合计是对所有支行进行计算的。

如果要按照支行进行分组，并统计每个支行的总合计数，则需要调换分组的顺序：
示例：

    SELECT product_cd, 
           open_branch_id, 
           SUM(avail_balance) tot_balance 
    FROM account 
    GROUP BY 2, 1 WITH ROLLUP;

返回数据：

product_cd | open_branch_id | tot_balance
-----|-----|-----
CD | 1 | 11500.00
CHK | 1 | 782.16
MM | 1 | 14832.64
SAV | 1 | 767.77
NULL | 1 | 27882.57
BUS | 2 | 9345.55
CD | 2 | 8000.00
CHK | 2 | 3315.77
SAV | 2 | 700.00
NULL | 2 | 21361.32
CHK | 3 | 1057.75
MM | 3 | 2212.50
SBL | 3 | 50000.00
NULL | 3 | 53270.25
BUS | 4 | 0.00
CHK | 4 | 67852.33
SAV | 4 | 387.99
NULL | 4 | 68240.32
NULL | NULL | 170754.46

此时5个额外的行，分别对应4个支行以及总合计数。

如果要同时计算产品合计和支行合计，那么可以使用WITH CUBE选项，它可以为分组列所有可能的组合产生合计行。

*MySQL没有实现WITH CUBE。*在SQL Server和Oracle中可以使用这个选项。

示例：*SQL Server*

    SELECT product_cd, 
           open_branch_id, 
           SUM(avail_balance) tot_balance 
    FROM account 
    GROUP BY 1, 2 WITH CUBE;

返回数据：

product_cd | open_branch_id | tot_balance
-----|-----|-----
NULL | NULL | 170754.46
NULL | 1 | 27882.57
NULL | 2 | 21361.32
NULL | 3 | 53270.25
NULL | 4 | 68240.32
BUS | 2 | 9345.55
BUS | 4 | 0.00
BUS | NULL | 9345.55
CD | 1 | 11500.00
CD | 2 | 8000.00
CD | NULL | 19500.00
CHK | 1 | 782.16
CHK | 2 | 3315.77
CHK | 3 | 1057.75
CHK | 4 | 67852.33
CHK | NULL | 73008.01
MM | 1 | 14832.64
MM | 3 | 2212.50
MM | NULL | 17045.14
SAV | 1 | 767.77
SAV | 2 | 700.00
SAV | 4 | 387.99
SAV | NULL | 1855.76
SBL | 3 | 50000.00
SBL | NULL | 50000.00

使用WITH CUBE的查询比使用WITH ROLLUP多产生4行，分别对应4个支行的合计数。

### 8.4 分组过滤条件

当分组数据时，也可以在产生分组后对数据应用过滤条件，HAVING子句就是放置这类过滤条件的底方。

    SELECT product_cd, 
           SUM(avail_balance) prod_balance 
    FROM account 
    WHERE STATUS = 'ACTIVE' 
    GROUP BY product_cd 
    HAVING SUM(avail_balance) >= 10000;

返回数据：

product_cd | prod_balance
-----|-----
CD | 19500.00
CHK | 73008.01
MM | 17045.14
SBL | 50000.00

该查询包含两个条件：

- WHERE子句：用于过滤掉不活动的账户，在分组之前执行
- HAVING子句：过滤可用余额合计小于10000的产品，在分组之后执行

如果将两个条件都放在WHERE子句中，会发生报错。

此外还可以在HAVING子句中包含未在SELECT语句中出现的聚集函数：

    SELECT product_cd, 
           SUM(avail_balance) prod_balance 
    FROM account 
    WHERE STATUS = 'ACTIVE' 
    GROUP BY product_cd 
    HAVING MIN(avail_balance) >= 1000 AND MAX(avail_balance) <= 10000;

返回数据：

product_cd | prod_balance
-----|-----
CD | 19500.00
MM | 17045.14

HAVING子句不一定要与GROUP BY子句联合使用，HAVING子句本身可以进行条件的判断。

示例：

    SELECT emp_id, CONCAT(fname, ' ', lname) FROM employee HAVING emp_id <= 5;

emp_id | concat(fname, ' ', lname)
----- | -----
1 | Michael Smith
2 | Susan Barker
3 | Robert Tyler
4 | Susan Hawthorne
5 | John Gooding

## 第九章 子查询 

>
子查询是一种可用于总共4种SQL语句的强大工具，本章讨论子查询的多种应用。

### 9.1 什么是子查询

子查询是指包含在另一个SQL语句（包含语句）内部的查询。子查询总是由括号包围，并且通常在包含语句之前执行。像其他查询一样，子查询也会返回一个如下类型的结果集：

- 单列单行
- 单列多行
- 多列多行

子查询返回的结果集类型决定了它可能如何被使用以及包含语句可能使用哪些运算来处理子查询返回的数据。任何子查询返回的数据在包含语句执行完成后都会被丢弃，这使子查询像一个具有作用域的临时表。

示例：

    SELECT account_id, 
           product_cd, 
           cust_id, 
           avail_balance 
    FROM account 
    WHERE account_id = (SELECT MAX(account_id) FROM account);

返回数据：

account_id | product_cd | cust_id | avail_balance
-----|-----|-----|-----
29 | SBL | 13 | 50000.00

上述示例种，子查询返回account表中account_id列的最大值，包含语句则返回对应账户的相关数据。

### 9.2 子查询类型

除了通过结果集类型划分子查询，还可以基于另外一个因素划分子查询：非关联子查询和关联查询

### 9.3 非关联子查询

非关联子查询完全独立，它可以单独执行而不需要引用包含语句中的任何内容。

前述的子查询除了是非关联子查询外，返回的都是一个单行单列的表，这种类型的子查询称为标量子查询，可以位于常用运算符的任意一边。

示例：

    SELECT account_id, 
           product_cd, cust_id, 
           avail_balance 
    FROM account 
    WHERE open_emp_id <> 
    (
        SELECT e.emp_id 
        FROM employee e 
        INNER JOIN branch b 
        ON e.assigned_branch_id = b.branch_id 
        WHERE e.title = 'Head Teller' AND b.city = 'Woburn'
    );

返回数据：

account_id | product_cd | cust_id | avail_balance
-----|-----|-----|-----
7 | CHK | 3 | 1057.75
8 | MM | 3 | 2212.50
10 | CHK | 4 | 534.12
11 | SAV | 4 | 767.77
12 | MM | 4 | 5487.09
13 | CHK | 5 | 2237.97
14 | CHK | 6 | 122.37
15 | CD | 6 | 10000.00
18 | CHK | 8 | 3487.19
19 | SAV | 8 | 387.99
21 | CHK | 9 | 125.67
22 | MM | 9 | 9345.55
23 | CD | 9 | 1500.00
24 | CHK | 10 | 23575.12
25 | BUS | 10 | 0.00
28 | CHK | 12 | 38552.05
29 | SBL | 13 | 50000.00

这个查询返回所有不是由Woburn分行的Head Teller开户的账户的相关数据。

由上例可以得出，子查询可以利用各种可用查询子句（SELECT、FROM、WHERE、GROUP BY、HAVING、ORDER BY）。

如果在等式条件下使用子查询，而子查询又返回多行，那么将会报错。

示例：

    SELECT account_id, 
           product_cd, 
           cust_id, 
           avail_balance 
    FROM account 
    WHERE open_emp_id <> 
    (
        SELECT * 
        FROM employee e 
        INNER JOIN branch b 
        ON e.assigned_branch_id = b.branch_id 
        WHERE e.title = 'Teller' AND b.city = 'Woburn'
    );

发生报错，原因是这个子查询会返回两行

    Error Code: 1241
    Operand should contain 1 column(s)

#### 9.3.1 多行单列子查询

返回多行结果的子查询不能再等式条件的一边使用。不过，另外有4个运算符可以用来为这些类型的子查询构建条件。

**IN和NOT IN运算符：**

虽然不能把一个值与一个值集进行相等比较，但是可以检查一个值集中能否包含某一个值。

示例：

    SELECT branch_id, NAME, city 
    FROM branch
    WHERE NAME IN ('Headquarters', 'Quincy Branch');

返回数据：

branch_id | name | city
-----|-----|-----
1 | Headquarters | Waltham
3 | Quincy Branch | Quincy

这里运算符IN的作用是检查name列值中是否有两个字符串中的任意一个：如果有，则条件满足，WHERE子句返回TRUE，该行被加入到结果集。

下面的查询使用IN运算符搭配子查询使用：

    SELECT emp_id, fname, lname, title 
    FROM employee
    WHERE emp_id IN
    (
        SELECT superior_emp_id FROM employee
    );

返回数据：

emp_id | fname | lname | title
-----|-----|-----|-----
1 | Michael | Smith | President
3 | Robert | Tyler | Treasurer
4 | Susan | Hawthorne | Operations Manager
6 | Helen | Fleming | Head Teller
10 | Paula | Roberts | Head Teller
13 | John | Blake | Head Teller
16 | Theresa | Markham | Head Teller

这个查询返回所有主管的相关信息。

除了判断一个值集中是否包含某一个值外，还可以使用NOT IN运算符来差选相反的情况，即是否不包含。

示例：

    SELECT emp_id, fname, lname, title 
    FROM employee
    WHERE emp_id NOT IN
    (
        SELECT superior_emp_id 
        FROM employee 
        WHERE superior_emp_id IS NOT NULL
    );

返回数据：

emp_id | fname | lname | title
-----|-----|-----|-----
2 | Susan | Barker | Vice President
5 | John | Gooding | Loan Manager
7 | Chris | Tucker | Teller
8 | Sarah | Parker | Teller
9 | Jane | Grossman | Teller
11 | Thomas | Ziegler | Teller
12 | Samantha | Jameson | Teller
14 | Cindy | Mason | Teller
15 | Frank | Portman | Teller
17 | Beth | Fowler | Teller
18 | Rick | Tulman | Teller

这个查询检索所有部管理别人的雇员。

**ALL运算符：**

IN运算符被用于查看是否能在一个表达式集合中找到某一个表达式，ALL运算符则用于将某单值与集合中的每个值进行比较。构建这样的条件需要将其中一个比较运算符与ALL运算符配合使用。

示例：

    SELECT emp_id, fname, lname, title 
    FROM employee
    WHERE emp_id <> ALL
    (
        SELECT superior_emp_id 
        FROM employee 
        WHERE superior_emp_id IS NOT NULL
    );

返回数据：

emp_id | fname | lname | title
-----|-----|-----|-----
2 | Susan | Barker | Vice President
5 | John | Gooding | Loan Manager
7 | Chris | Tucker | Teller
8 | Sarah | Parker | Teller
9 | Jane | Grossman | Teller
11 | Thomas | Ziegler | Teller
12 | Samantha | Jameson | Teller
14 | Cindy | Mason | Teller
15 | Frank | Portman | Teller
17 | Beth | Fowler | Teller
18 | Rick | Tulman | Teller

这个查询同样检索所有不管理别人的雇员。

*当使用NOT IN或<>运算符比较一个值和一个值集时，必须注意确保值集中不包含NULL值。这是因为服务器将表达式左边的值与值集中的每个成员进行比较时，可能出现该值与NULL比较的情况，而任何一个值与NULL进行比较都将会产生未知的结果。*

示例：这个查询会返回空结果集

    SELECT emp_id FROM employee WHERE emp_id NOT IN (1, 2, NULL);

使用ALL运算符的另一个示例：查询账户可用余额小于“Frank Tucker”账户余额的账户

    SELECT account_id, cust_id, product_cd, avail_balance 
    FROM account
    WHERE avail_balance < ALL
    (
        SELECT a.avail_balance 
        FROM account a 
        INNER JOIN individual i
        ON a.cust_id = i.cust_id
        WHERE i.fname = 'Frank' AND i.lname = 'Tucker'
    );

返回数据：

account_id | cust_id | product_cd | avail_balance
-----|-----|-----|-----
2 | 1 | SAV | 500.00
5 | 2 | SAV | 200.00
10 | 4 | CHK | 534.12
11 | 4 | SAV | 767.77
14 | 6 | CHK | 122.37
19 | 8 | SAV | 387.99
21 | 9 | CHK | 125.67
25 | 10 | BUS | 0.00

上述查询也可以不用ALL运算符实现：

    SELECT account_id, cust_id, product_cd, avail_balance 
    FROM account
    WHERE avail_balance < 
    (
        SELECT MIN(a.avail_balance) 
        FROM account a 
        INNER JOIN individual i
        ON a.cust_id = i.cust_id
        WHERE i.fname = 'Frank' AND i.lname = 'Tucker'
    );

**ANY运算符：**
与ALL运算符一样，ANY运算符允许将一个值与值集中的每个成员相比较。与ALL不同的是，使用ANY运算符时，只要有一个比较成立，则条件为真；使用ALL运算符时，只有与集合中所有成员比较都成立时条件才为真。

示例：

    SELECT account_id, cust_id, product_cd, avail_balance 
    FROM account
    WHERE avail_balance > ANY
    (
        SELECT a.avail_balance 
        FROM account a 
        INNER JOIN individual i
        ON a.cust_id = i.cust_id
        WHERE i.fname = 'Frank' AND i.lname = 'Tucker'
    );

返回数据：

account_id | cust_id | product_cd | avail_balance
-----|-----|-----|-----
3 | 1 | CD | 3000.00
4 | 2 | CHK | 2258.02
8 | 3 | MM | 2212.50
12 | 4 | MM | 5487.09
13 | 5 | CHK | 2237.97
15 | 6 | CD | 10000.00
17 | 7 | CD | 5000.00
18 | 8 | CHK | 3487.19
22 | 9 | MM | 9345.55
23 | 9 | CD | 1500.00
24 | 10 | CHK | 23575.12
27 | 11 | BUS | 9345.55
28 | 12 | CHK | 38552.05
29 | 13 | SBL | 50000.00

*=ANY与IN等效，<>ALL与NOT IN等效。*

#### 9.3.2 多列子查询

前述的子查询示例都是返回单列单行或者多行结果。不过在某些情况下，我们可以使用返回两列或者多列的子查询，为了展示多列子查寻得用途，先来看一个多重多列子查询的例子。

示例：

    SELECT account_id, product_cd, cust_id
    FROM account
    WHERE open_branch_id = 
    (
        SELECT branch_id 
        FROM branch 
        WHERE NAME = 'Woburn Branch'
    )
    AND open_emp_id IN
    (
        SELECT emp_id
        FROM employee
        WHERE title IN ('Teller', 'Head Teller')
    );

返回数据：

account_id | product_cd | cust_id
-----|-----|-----
1 | CHK | 1
2 | SAV | 1
3 | CD | 1
4 | CHK | 2
5 | SAV | 2
17 | CD | 7
27 | BUS | 11

上述这个查询使用两个子查询检索出Woburn支行的ID以及所有银行柜台员工的ID，同时包含查询使用这个信息查找所有Woburn支行柜员开设的账户。

不过，既然employee表包含每个柜员所属分行的信息，那么将`account.open_branch_id`和`account.open_emp_id`两列与对employee和branch两个表的单一子查询结果作比较也可以获得同样的结果。

示例：

    SELECT account_id, product_cd, cust_id
    FROM account
    WHERE (open_branch_id, open_emp_id) IN
    (
        SELECT b.branch_id, e.emp_id
        FROM branch b 
        INNER JOIN employee e
        ON b.branch_id = e.assigned_branch_id
        WHERE b.name = 'Woburn Branch'
        AND e.title IN ('Teller', 'Head Teller')
    );

这种方式的查询功能与前面的例子相同，只不过是使用返回两列的单一查询代替两个只返回单列的子查询。

### 9.4 关联子查询

前述的所有组查询都是独立于包含语句的，这意味着这些子查询可以单独执行，并可检验结果。

与非关联子查询不同，关联子查询不是在包含语句执行之前一次执行完毕，而是为每一个候选行（这些行可能会包含在最终结果里）执行一次。

示例：

    SELECT c.cust_id, c.cust_type_cd, c.city
    FROM customer c
    WHERE 2 = 
    (
        SELECT COUNT(*)
        FROM account a
        WHERE a.cust_id = c.cust_id
    );

返回数据：

cust_id | cust_type_cd | city
-----|-----|-----
2 | I | Woburn
3 | I | Quincy
6 | I | Waltham
8 | I | Salem
10 | B | Salem

上述查询首先利用关联查询计算每个各户的账户数，接着包含查询检索出哪些拥有两个账户的客户。

这里子查询最后引用的`c.cust_id`使之具有关联性，这样它的执行必须依赖于包含查询提供的`c.cust_id`。在这种情况下，先从custmoer表中检索出13行客户记录，接着为每个客户执行一次子查询，每次执行包含查询都要向子查询传递客户ID。若子查询返回值为2，则满足过滤条件，该行将被添加到结果集中。

除了等式条件，关联子查询还可以用于其他类型的条件。

示例：

    SELECT c.cust_id, c.cust_type_cd, c.city
    FROM customer c
    WHERE
    (
        SELECT SUM(a.avail_balance)
        FROM account a
        WHERE a.cust_id = c.cust_id
    ) BETWEEN 5000 AND 10000;

返回数据：

cust_id | cust_type_cd | city
-----|-----|-----
4 | I | Waltham
7 | I | Wilmington
11 | B | Wilmington

上述查询检索账户总余额在5000~10000的客户信息。

#### 9.4.1 EXISTS运算符

若只关心存在关系而不在乎数量，就可以使用EXISTS运算符。该运算符是构造包含关联子查询的最常用运算符。

示例：

    SELECT a.account_id, a.product_cd, a.cust_id, a.avail_balance
    FROM account a
    WHERE EXISTS
    (
        SELECT 1 
        FROM TRANSACTION t
        WHERE t.account_id = a.account_id
        AND t.txn_date = '2004-06-30'
    );

返回数据：

account_id | product_cd | cust_id | avail_balance
-----|-----|-----|-----
3 | CD | 1 | 3000.00
23 | CD | 9 | 1500.00

上述查询检索在指定日期进行过交易的所有账户，并不关心交易了多少次。

**当子查询根据其查询条件确实可以返回数据（不限定行数）时，EXISTS运算符会返回TRUE。此时包含查询的当前行会被加入到结果集。反之，如果子查询不返回任何数据，则EXISTS运算符会返回FALSE，导致包含查询的当前行不加入结果集。**

实际上，这里子查询可以写任意内容：

    SELECT a.account_id, a.product_cd, a.cust_id, a.avail_balance
    FROM account a
    WHERE EXISTS
    (
        SELECT t.txn_id, 'Hello', 3.1415
        FROM TRANSACTION t
        WHERE t.account_id = a.account_id
        AND t.txn_date = '2004-06-30'
    );

不过惯例上使用`SELECT 1`或`SELECT *`。

同样地，我们也可以使用NOT EXISTS运算符来检验相反的条件：

    SELECT a.account_id, a.product_cd, a.cust_id
    FROM account a
    WHERE NOT EXISTS
    (
        SELECT *
        FROM business b
        WHERE b.cust_id = a.cust_id
    );

返回结果：

account_id | product_cd | cust_id
-----|-----|-----
1 | CHK | 1
2 | SAV | 1
3 | CD | 1
4 | CHK | 2
5 | SAV | 2
7 | CHK | 3
8 | MM | 3
10 | CHK | 4
11 | SAV | 4
12 | MM | 4
13 | CHK | 5
14 | CHK | 6
15 | CD | 6
17 | CD | 7
18 | CHK | 8
19 | SAV | 8
21 | CHK | 9
22 | MM | 9
23 | CD | 9

上述查询检索了客户ID没有出现在business表中的所有客户。

#### 9.4.2 关联子查询操作数据

到目前为止，所有例子都是SELECT语句，但这并不意味着子查询在其他SQL语句中没有用处。

**子查询同样可以应用于UPDATE、DELETE和INSERT语句**

示例：

    UPDATE account a
    SET a.last_activity_date =
    (
        SELECT MAX(t.txn_date)
        FROM TRANSACTION t
        WHERE t.account_id = a.account_id
    )
    WHERE EXISTS
    (
        SELECT * 
        FROM TRANSACTION t
        WHERE t.account_id = a.account_id
    );

上述语句查找每个账户的最新交易日期，再修改account表中对应行的last_activity_date列的值。不过SET子句的子查询仅当WHERE子句为真时才执行（这意味着至少能查找到一次这个账户的交易记录），这样可以保护last_activity_date列不被NULL重写（如果子查询没有返回任何数据，SET子句将把last_activity_date的值设置为NULL）。

关联子查询也经常用于DELETE语句。

示例：

    DELETE FROM department
    WHERE NOT EXISTS
    (
        SELECT *
        FROM employee 
        WHERE employee.dept_id = department.dept_id
    );

上述语句从的department表中删除dept_id没有出现在employee表中的记录。

*MySQL中DELETE语句使用关联子查询时不能使用表别名。*

我们还可以使用子查询对表或者表的一部分进行复制以创建新的表。

示例：

    CREATE TABLE employee_tmp AS
    (
        SELECT emp_id, CONCAT(fname, ' ', lname), title
        FROM employee
        WHERE emp_id <= 5
    );

此时创建了新表：

emp_id | name | title
-----|-----|-----
1 | Michael Smith | President
2 | Susan Barker | Vice President
3 | Robert Tyler | Treasurer
4 | Susan Hawthorne | Operations Manager
5 | John Gooding | Loan Manager

然后采用子查询的方式向这个新表添加数据：

    INSERT INTO employee_tmp (emp_id, NAME, title)
    (
        SELECT emp_id, CONCAT(fname, ' ', lname), title
        FROM employee
        WHERE emp_id = 10
    );

此时表中数据为：

emp_id | name | title
-----|-----|-----
1 | Michael Smith | President
2 | Susan Barker | Vice President
3 | Robert Tyler | Treasurer
4 | Susan Hawthorne | Operations Manager
5 | John Gooding | Loan Manager
10 | Paula Roberts | Head Teller

### 9.5 何时使用子查询

#### 9.5.1 子查询作为数据源

子查询可以作为一个临时表，放在FROM子句中作为数据源。

示例：

    SELECT d.dept_id, d.name, e_cnt.how_many num_employees
    FROM department d
    INNER JOIN
    (
        SELECT dept_id, COUNT(*) how_many
        FROM employee
        GROUP BY dept_id
    ) e_cnt
    ON d.dept_id = e_cnt.dept_id;

返回数据：

dept_id | name | num_employees
-----|-----|-----
1 | Operations | 14
2 | Loans | 1
3 | Administration | 3

上述语句查询了每个部门的ID、名字及雇员数。

**子查询在FROM子句中使用时必须是非关联的，它们首先执行，然后一直保留在内存中直至包含查询执行完毕。**

**数据加工：**
除了使用查询总结现有数据，我们还可以生成数据库中不存在的数据。

示例：

假设打算按照储蓄账户余额的多少对客户进行分组，但是这些组的定义没有存储在数据库中。

分组情况：

组名 | 下限值 | 上限值
-----|-----|-----
Small Fry | 0 | $4999.99
Average Joes | $5000 | $9999.99
Heavy Hitters | $10000 | $9999999.99

为了在单一查询里生成这些组，我们需要一种方法来定义这些组。

定义一个用于生成组定义的查询：

    SELECT 'Small Fry' NAME, 0 low_limit, 4999.99 High_limit
    UNION ALL
    SELECT 'Average Joes' NAME, 5000 low_limit, 9999.99 High_limit
    UNION ALL
    SELECT 'Heavy Hitters' NAME, 10000 low_limit, 9999999.99 High_limit;

返回数据：

name | low_limit | High_limit
-----|-----|-----
Small Fry | 0 | 4999.99
Average Joes | 5000 | 9999.99
Heavy Hitters | 10000 | 9999999.99

现在已经有了生成组的查询，只要将它添加到另一个查询的FROM子句中就可以了：

    SELECT groups.name, COUNT(*) num_customers
    FROM 
    (
        SELECT a.cust_id, SUM(a.avail_balance) cust_balance
        FROM account a
        INNER JOIN product p
        ON a.product_cd = p.product_cd
        WHERE p.product_type_cd = 'ACCOUNT'
        GROUP BY a.cust_id
    ) cust_rollup
    INNER JOIN 
    (
        SELECT 'Small Fry' NAME, 0 low_limit, 4999.99 High_limit
        UNION ALL
        SELECT 'Average Joes' NAME, 5000 low_limit, 9999.99 High_limit
        UNION ALL
        SELECT 'Heavy Hitters' NAME, 10000 low_limit, 9999999.99 High_limit
    ) groups
    ON cust_rollup.cust_balance
    BETWEEN groups.low_limit AND groups.high_limit
    GROUP BY groups.name;

返回数据：

name | num_customers
-----|-----
Average Joes | 2
Heavy Hitters | 4
Small Fry | 5

上述查询语句的FROM子句共有两个子查询：第一个名为cust_rollup，返回每个客户的储蓄余额总和；另一个名为groups，生成三个分组。

cust_rollup生成的数据如下：

cust_id | cust_balance
-----|-----
1 | 4557.75
2 | 2458.02
3 | 3270.25
4 | 6788.98
5 | 2237.97
6 | 10122.37
7 | 5000.00
8 | 3875.18
9 | 10971.22
10 | 23575.12
12 | 38552.05

然后cust_rollup生成的数据通过范围条件（`cust_rollup.cust_balance BETWEEN groups.low_limit AND groups.high_limit`）与groups连接。最后，连接的数据被分组并计算每个组的客户数，进而产生最后的结果集。

*当然我们也可以创建一个固定的表来描述组的定义。不过如果使用这种方法，那么不久后就可能会发现数据库因为这些用于特殊目的的表而变得杂乱，并且很可能会忘记大多数表是为了什么而创建的。*

我们应当坚持这样的原则：**仅当有明确的商业需求保存这些新数据时才能添加相应的新表到数据库。**

**面向任务的子查询：**

假设有以下查询：

    SELECT p.name product,
           b.name branch,
           CONCAT(e.fname, ' ', e.lname) NAME,
           SUM(a.avail_balance) tot_deposits
    FROM account a
    INNER JOIN employee e
    ON a.open_emp_id = e.emp_id
    INNER JOIN branch b
    ON a.open_branch_id = b.branch_id
    INNER JOIN product p
    ON a.product_cd = p.product_cd
    WHERE p.product_type_cd = 'ACCOUNT'
    GROUP BY p.name, b.name, NAME
    ORDER BY 1, 2;

返回数据：

product | branch | name | tot_deposits
-----|-----|-----|-----
certificate of deposit | Headquarters | Michael Smith | 11500.00
certificate of deposit | Woburn Branch | Paula Roberts | 8000.00
checking account | Headquarters | Michael Smith | 782.16
checking account | Quincy Branch | John Blake | 1057.75
checking account | So. NH Branch | Theresa Markham | 67852.33
checking account | Woburn Branch | Paula Roberts | 3315.77
money market account | Headquarters | Michael Smith | 14832.64
money market account | Quincy Branch | John Blake | 2212.50
savings account | Headquarters | Michael Smith | 767.77
savings account | So. NH Branch | Theresa Markham | 387.99
savings account | Woburn Branch | Paula Roberts | 700.00

上面的查询依据账户类型、开户支行和开户柜员对所有储蓄账户余额进行求和。

我们可以发现表product、branch、employee只是用于描述目的的，并且account表已经有了生成分组（product_cd、open_branch_id、open_emp_id和avail_balance）所需的一切。我已我们可以把生成分组的任务独立出来，生成一个子查询，然后将子查询生成的表再与product、branch、employee进行连接。

示例：分组的子查询

    SELECT product_cd, 
           open_branch_id branch_id,
           open_emp_id emp_id,
           SUM(avail_balance) tot_deposits
    FROM account
    GROUP BY 1, 2, 3;

返回数据：

product_cd | branch_id | emp_id | tot_deposits
-----|-----|-----|-----
BUS | 2 | 10 | 9345.55
BUS | 4 | 16 | 0.00
CD | 1 | 1 | 11500.00
CD | 2 | 10 | 8000.00
CHK | 1 | 1 | 782.16
CHK | 2 | 10 | 3315.77
CHK | 3 | 13 | 1057.75
CHK | 4 | 16 | 67852.33
MM | 1 | 1 | 14832.64
MM | 3 | 13 | 2212.50
SAV | 1 | 1 | 767.77
SAV | 2 | 10 | 700.00
SAV | 4 | 16 | 387.99
SBL | 3 | 13 | 50000.00

将上述查询包装在一个子查询中， 并将其结果表与其他3个表相连：

    SELECT p.name product,
           b.name branch,
           CONCAT(e.fname, ' ', e.lname) NAME,
           account_groups.tot_deposits
    FROM 
    (
        SELECT product_cd, 
               open_branch_id branch_id,
               open_emp_id emp_id,
               SUM(avail_balance) tot_deposits
        FROM account
        GROUP BY 1, 2, 3
    ) account_groups
    INNER JOIN employee e
    ON e.emp_id = account_groups.emp_id
    INNER JOIN branch b
    ON b.branch_id = account_groups.branch_id
    INNER JOIN product p
    ON p.product_cd = account_groups.product_cd
    WHERE p.product_type_cd = 'ACCOUNT'
    ORDER BY 1, 2;

#### 9.5.2 过滤条件中的子查询

作为过滤条件是子查询的主要作用之一。不过，作为过滤条件的子查询并不会只出现在WHERE子句中。

示例：

    SELECT open_emp_id, COUNT(*) how_many
    FROM account
    GROUP BY 1
    HAVING COUNT(*) = 
    (
        SELECT MAX(emp_cnt.how_many)
        FROM 
        (
            SELECT COUNT(*) how_many
            FROM account
            GROUP BY open_emp_id
        ) emp_cnt
    );

返回数据：

open_emp_id | how_many
-----|-----
1 | 8

HAVING子句中的子查询检索所有雇员的最大开户数，包含查询则查找这个开户数最多的雇员。如果有多人并列开户数最高，那么查询将返回多行。

#### 9.5.3 子查询作为表达式生成器

单列单行的标量子查询除了用于过滤条件外，还能用在表达式可以出现的任何位置。其中包括查询中的SELECT和ORDER BY子句以及INSERT语句中的VALUES子句。

下面的查询是“面向任务的子查询”的另一个版本：

    SELECT 
    (
        SELECT p.name 
        FROM product p
        WHERE p.product_cd = a.product_cd
        AND p.product_type_cd = 'ACCOUNT' 
    ) product,
    (
        SELECT b.name 
        FROM branch b
        WHERE b.branch_id = a.open_branch_id
    ) branch,
    (
        SELECT CONCAT(e.fname, ' ', e.lname)
        FROM employee e
        WHERE e.emp_id = a.open_emp_id
    ) NAME,
    SUM(a.avail_balance) tot_deposits
    FROM account a
    GROUP BY a.product_cd, a.open_branch_id, a.open_emp_id
    ORDER BY 1, 2;

返回数据：

product | branch | name | tot_deposits
-----|-----|-----|-----
NULL | Quincy Branch | John Blake | 50000.00
NULL | So. NH Branch | Theresa Markham | 0.00
NULL | Woburn Branch | Paula Roberts | 9345.55
certificate of deposit | Headquarters | Michael Smith | 11500.00
certificate of deposit | Woburn Branch | Paula Roberts | 8000.00
checking account | Headquarters | Michael Smith | 782.16
checking account | Quincy Branch | John Blake | 1057.75
checking account | So. NH Branch | Theresa Markham | 67852.33
checking account | Woburn Branch | Paula Roberts | 3315.77
money market account | Headquarters | Michael Smith | 14832.64
money market account | Quincy Branch | John Blake | 2212.50
savings account | Headquarters | Michael Smith | 767.77
savings account | So. NH Branch | Theresa Markham | 387.99
savings account | Woburn Branch | Paula Roberts | 700.00

这个查询与前述的查询的最大区别在于它在SELECT子句中使用了子查询：

- 不是将product、branch和employee与账户数据进行连接，而是在SELECT子句中使用关联标量子查询查找产品、分行和雇员的名字。
- 结果有14行而不是11行，其中3个产品的名称为NULL。

下面将三行NULL排除：

    SELECT all_prods.product,
           all_prods.branch,
           all_prods.name,
           all_prods.tot_deposits
    FROM
    (
        SELECT -- 该查询会返回3个NULL行
        (
            SELECT p.name 
            FROM product p
            WHERE p.product_cd = a.product_cd
            AND p.product_type_cd = 'ACCOUNT'
        ) product,
        (
            SELECT b.name 
            FROM branch b
            WHERE b.branch_id = a.open_branch_id
        ) branch,
        (
            SELECT CONCAT(e.fname, ' ', e.lname)
            FROM employee e
            WHERE e.emp_id = a.open_emp_id
        ) NAME,
        SUM(a.avail_balance) tot_deposits
        FROM account a
        GROUP BY a.product_cd, a.open_branch_id, a.open_emp_id
    ) all_prods
    WHERE all_prods.product IS NOT NULL
    ORDER BY 1, 2;

这个查询将前述的查询包装为一个子查询，然后添加过滤条件，排除product为NULL的行。

或者可以简单的添加过滤条件：

添加WHERE子句：

    SELECT 
    (
        SELECT p.name 
        FROM product p
        WHERE p.product_cd = a.product_cd
        AND p.product_type_cd = 'ACCOUNT' 
    ) product,
    (
        SELECT b.name 
        FROM branch b
        WHERE b.branch_id = a.open_branch_id
    ) branch,
    (
        SELECT CONCAT(e.fname, ' ', e.lname)
        FROM employee e
        WHERE e.emp_id = a.open_emp_id
    ) NAME,
    SUM(a.avail_balance) tot_deposits
    FROM account a
    WHERE -- 直接使用product IS NOT NULL会报错，因为account表中无product列
    (
        SELECT p.name 
        FROM product p
        WHERE p.product_cd = a.product_cd
        AND p.product_type_cd = 'ACCOUNT' 
    ) IS NOT NULL
    GROUP BY a.product_cd, a.open_branch_id, a.open_emp_id
    ORDER BY 1, 2;

添加HAVING子句：

    SELECT 
    (
        SELECT p.name 
        FROM product p
        WHERE p.product_cd = a.product_cd
        AND p.product_type_cd = 'ACCOUNT' 
    ) product,
    (
        SELECT b.name 
        FROM branch b
        WHERE b.branch_id = a.open_branch_id
    ) branch,
    (
        SELECT CONCAT(e.fname, ' ', e.lname)
        FROM employee e
        WHERE e.emp_id = a.open_emp_id
    ) NAME,
    SUM(a.avail_balance) tot_deposits
    FROM account a
    GROUP BY a.product_cd, a.open_branch_id, a.open_emp_id
    HAVING product IS NOT NULL
    ORDER BY 1, 2;

标量子查询同样可以出现在ORDER BY子句中。

示例：

    SELECT emp.emp_id,
           CONCAT(emp.fname, ' ', emp.lname) emp_name,
           (
                SELECT CONCAT(boss.fname, ' ', boss.lname) 
                FROM employee boss
                WHERE boss.emp_id = emp.superior_emp_id
           ) boss_name
    FROM employee emp
    WHERE emp.superior_emp_id IS NOT NULL
    ORDER BY
    (
        SELECT boss.lname 
        FROM employee boss
        WHERE boss.emp_id = emp.superior_emp_id
    ), emp.lname;

返回数据：

emp_id | emp_name | boss_name
-----|-----|-----
14 | Cindy Mason | John Blake
15 | Frank Portman | John Blake
9 | Jane Grossman | Helen Fleming
8 | Sarah Parker | Helen Fleming
7 | Chris Tucker | Helen Fleming
13 | John Blake | Susan Hawthorne
6 | Helen Fleming | Susan Hawthorne
5 | John Gooding | Susan Hawthorne
16 | Theresa Markham | Susan Hawthorne
10 | Paula Roberts | Susan Hawthorne
17 | Beth Fowler | Theresa Markham
18 | Rick Tulman | Theresa Markham
12 | Samantha Jameson | Paula Roberts
11 | Thomas Ziegler | Paula Roberts
2 | Susan Barker | Michael Smith
3 | Robert Tyler | Michael Smith
4 | Susan Hawthorne | Robert Tyler

这个查询使用了两个关联标量子查询：一个用于检索每个雇员老板姓名的SELECT子句，另一个用于排序而只返回雇员老板姓氏的ORDER BY子句。

我们同样可以在INSERT语句中使用关联标量子查询。

示例，向account表中添加新的值：

- 产品名称：savings account
- 客户个人身份识别码：555-55-5555
- 开户行名称：Quincy Branch
- 开户柜员姓名：Frank Portman

使用子查询生成值：

    INSERT INTO account 
    VALUES 
    (
        NULL, (SELECT product_cd FROM product WHERE NAME = 'savings account'),
        (SELECT cust_id FROM customer WHERE fed_id = '555-55-5555'),
        '2008-09-25', NULL, '2008-09-25', 'ACTIVE',
        (SELECT branch_id FROM branch WHERE NAME = 'Quincy Branch'),
        (SELECT emp_id FROM employee WHERE lname = 'Portman' AND fname = 'Frank'),
        0, 0
    );

这种方法有一个缺点，当要插入的列允许NULL值时，即使子查询不能返回值，INSERT语句也会成功。例如如果拼错了Frank Portman，account表依然会创建一个新行，但此时该行的open_emp_id值为NULL。

## 第十章 再谈连接

>
本章讨论外连接和交叉连接在内的其他连接表的方法。

### 10.1 外连接

到目前为止，所述的连接都包含多个表，在此种情况下我们没有考虑连接条件可能无法为表中所有行匹配的问题。如果真的发生设个问题，则一个表或其他表中的某些行就可能被遗漏在结果集之外。

要确定是否存在上述问题，我们需要检查表中的数据。

示例：检查account表中account_id列和cust_id列

    SELECT account_id, cust_id FROM account;

返回数据：

account_id | cust_id
-----|-----
1 | 1
2 | 1
3 | 1
4 | 2
5 | 2
7 | 3
8 | 3
10 | 4
11 | 4
12 | 4
13 | 5
14 | 6
15 | 6
17 | 7
18 | 8
19 | 8
21 | 9
22 | 9
23 | 9
24 | 10
25 | 10
27 | 11
28 | 12
29 | 13

由结果可知，共24个账户，13个不同客户，每个客户至少有一个账户。

由于customer表中的ID共有13行，分布在1~13，因此account表中包含每个客户ID至少一次。所以在两个表使用cust_id连接时，期望的结果应该是24行。

    SELECT a.account_id, c.cust_id
    FROM account a
    INNER JOIN customer c
    USING (cust_id);

返回数据：

account_id | cust_id
-----|-----
1 | 1
2 | 1
3 | 1
4 | 2
5 | 2
7 | 3
8 | 3
10 | 4
11 | 4
12 | 4
13 | 5
14 | 6
15 | 6
17 | 7
18 | 8
19 | 8
21 | 9
22 | 9
23 | 9
24 | 10
25 | 10
27 | 11
28 | 12
29 | 13

再将account表连接至business表：

    SELECT a.account_id, b.cust_id, b.name
    FROM account a
    INNER JOIN business b
    USING (cust_id);

返回数据：

account_id | cust_id | name
-----|-----|-----
24 | 10 | Chilton Engineering
25 | 10 | Chilton Engineering
27 | 11 | Northeast Cooling Inc.
28 | 12 | Superior Auto Body
29 | 13 | AAA Insurance Inc.

结果只返回5行。这是因为business表只有4行数据，又因为其中一个顾客拥有两个账户，所以当account表与business使用内连接时，总共只有5行发生了连接。

如果要查询返回所有账户，但只返回拥有这些账户的商业客户的名字，这时需要使用外连接：

    SELECT a.account_id, b.cust_id, b.name
    FROM account a
    LEFT OUTER JOIN business b
    ON a.cust_id = b.cust_id;

返回数据：

account_id | cust_id | name
-----|-----|-----
24 | 10 | Chilton Engineering
25 | 10 | Chilton Engineering
27 | 11 | Northeast Cooling Inc.
28 | 12 | Superior Auto Body
29 | 13 | AAA Insurance Inc.
1 | NULL | NULL
2 | NULL | NULL
3 | NULL | NULL
4 | NULL | NULL
5 | NULL | NULL
7 | NULL | NULL
8 | NULL | NULL
10 | NULL | NULL
11 | NULL | NULL
12 | NULL | NULL
13 | NULL | NULL
14 | NULL | NULL
15 | NULL | NULL
17 | NULL | NULL
18 | NULL | NULL
19 | NULL | NULL
21 | NULL | NULL
22 | NULL | NULL
23 | NULL | NULL

外连接包括第一个表的所有行，但仅仅包含第二个表中那些匹配行的数据。由于指定的连接方式为LEFT OUTER JOIN，所以左表account的所有行被包含。结果集中除了4个商业客户，其余所有行的name值均为NULL。

#### 10.1.1 左外连接与右外联连接

关键字LEFT指出连接左边的表决定结果集的行数，而右边的只负责提供与之匹配的列值。

示例：

    SELECT c.cust_id, b.name
    FROM customer c
    LEFT OUTER JOIN business b
    USING (cust_id);

返回数据：

cust_id | name
-----|-----
10 | Chilton Engineering
11 | Northeast Cooling Inc.
12 | Superior Auto Body
13 | AAA Insurance Inc.
1 | NULL
2 | NULL
3 | NULL
4 | NULL
5 | NULL
6 | NULL
7 | NULL
8 | NULL
9 | NULL

FROM子句指定了一个左外连接，因此customer表的所有行都包含在结果集中，而business表将4个商业客户的名字填入结果集的第二列。

关键字RIGHT指出连接右边的表决定结果集的行数，而左边的只负责提供与之匹配的列值。

示例：

    SELECT c.cust_id, b.name
    FROM customer c
    RIGHT OUTER JOIN business b
    USING (cust_id);

返回数据：

cust_id | name
-----|-----
10 | Chilton Engineering
11 | Northeast Cooling Inc.
12 | Superior Auto Body
13 | AAA Insurance Inc.

现在结果集的行数由business表决定，因此结果集只有4行。

*A LEFT OUTER JOIN B 等价于 B RIGHT OUTER JOIN A。*

#### 10.1.2 三路外连接

示例：查询所有账户列表，其中包含个人客户的姓名以及商业客户的企业名称

    SELECT a.account_id, 
           CONCAT(i.fname, ' ', i.lname) person_name,
           b.name business_name
    FROM account a
    LEFT OUTER JOIN individual i USING (cust_id)
    LEFT OUTER JOIN business b USING (cust_id)
    ORDER BY 1;

返回数据：

account_id | person_name | business_name
-----|-----|-----
1 | James Hadley | NULL
2 | James Hadley | NULL
3 | James Hadley | NULL
4 | Susan Tingley | NULL
5 | Susan Tingley | NULL
7 | Frank Tucker | NULL
8 | Frank Tucker | NULL
10 | John Hayward | NULL
11 | John Hayward | NULL
12 | John Hayward | NULL
13 | Charles Frasier | NULL
14 | John Spencer | NULL
15 | John Spencer | NULL
17 | Margaret Young | NULL
18 | Louis Blake | NULL
19 | Louis Blake | NULL
21 | Richard Farley | NULL
22 | Richard Farley | NULL
23 | Richard Farley | NULL
24 | NULL | Chilton Engineering
25 | NULL | Chilton Engineering
27 | NULL | Northeast Cooling Inc.
28 | NULL | Superior Auto Body
29 | NULL | AAA Insurance Inc.

我们也可以使用子查询修改上述查询的SQL语句：

    SELECT account_ind.account_id,
           account_ind.person_name,
           b.name business_name
    FROM 
    (
        SELECT a.account_id,
               a.cust_id,
               CONCAT(i.fname, ' ', i.lname) person_name
        FROM account a
        LEFT OUTER JOIN individual i
        USING (cust_id)
    ) account_ind
    LEFT OUTER JOIN business b
    USING (cust_id)
    ORDER BY 1;

这样写的好处是降低了对同一个表的外连接次数，每个查询只使用了单一外连接。

#### 10.1.3 自外连接

下面是一个自链接示例：将employee表连接到自己而生成雇员和它们主管的表

    SELECT e.fname, e.lname, e_mgr.fname mgr_fname, e_mgr.lname mgr_lname
    FROM employee e
    INNER JOIN employee e_mgr
    ON e.superior_emp_id = e_mgr.emp_id;

返回数据：

fname | lname | mgr_fname | mgr_lname
-----|-----|-----|-----
Susan | Barker | Michael | Smith
Robert | Tyler | Michael | Smith
Susan | Hawthorne | Robert | Tyler
John | Gooding | Susan | Hawthorne
Helen | Fleming | Susan | Hawthorne
Chris | Tucker | Helen | Fleming
Sarah | Parker | Helen | Fleming
Jane | Grossman | Helen | Fleming
Paula | Roberts | Susan | Hawthorne
Thomas | Ziegler | Paula | Roberts
Samantha | Jameson | Paula | Roberts
John | Blake | Susan | Hawthorne
Cindy | Mason | John | Blake
Frank | Portman | John | Blake
Theresa | Markham | Susan | Hawthorne
Beth | Fowler | Theresa | Markham
Rick | Tulman | Theresa | Markham

这个查询存在一个问题：没有主管的雇员被排除在了结果集之外。

使用外连接修正上述错误：

    SELECT e.fname, e.lname, e_mgr.fname mgr_fname, e_mgr.lname mgr_lname
    FROM employee e
    LEFT OUTER JOIN employee e_mgr
    ON e.superior_emp_id = e_mgr.emp_id;

返回数据：

fname | lname | mgr_fname | mgr_lname
-----|-----|-----|-----
Michael | Smith | NULL | NULL
Susan | Barker | Michael | Smith
Robert | Tyler | Michael | Smith
Susan | Hawthorne | Robert | Tyler
John | Gooding | Susan | Hawthorne
Helen | Fleming | Susan | Hawthorne
Chris | Tucker | Helen | Fleming
Sarah | Parker | Helen | Fleming
Jane | Grossman | Helen | Fleming
Paula | Roberts | Susan | Hawthorne
Thomas | Ziegler | Paula | Roberts
Samantha | Jameson | Paula | Roberts
John | Blake | Susan | Hawthorne
Cindy | Mason | John | Blake
Frank | Portman | John | Blake
Theresa | Markham | Susan | Hawthorne
Beth | Fowler | Theresa | Markham
Rick | Tulman | Theresa | Markham

### 10.2 交叉连接

交叉连接 - - - 笛卡尔积，本质上就是不指定任何连接条件的情况下多表连接的结果。

示例：

    SELECT pt.name, p.product_cd, p.name 
    FROM product p 
    CROSS JOIN product_type pt;

或者：

    SELECT pt.name, p.product_cd, p.name 
    FROM product p, product_type pt;

返回数据：

name | product_cd | name
-----|-----|-----
Customer Accounts | AUT | auto loan
Insurance Offerings | AUT | auto loan
Individual and Business Loans | AUT | auto loan
Customer Accounts | BUS | business line of credit
Insurance Offerings | BUS | business line of credit
Individual and Business Loans | BUS | business line of credit
Customer Accounts | CD | certificate of deposit
Insurance Offerings | CD | certificate of deposit
Individual and Business Loans | CD | certificate of deposit
Customer Accounts | CHK | checking account
Insurance Offerings | CHK | checking account
Individual and Business Loans | CHK | checking account
Customer Accounts | MM | money market account
Insurance Offerings | MM | money market account
Individual and Business Loans | MM | money market account
Customer Accounts | MRT | home mortgage
Insurance Offerings | MRT | home mortgage
Individual and Business Loans | MRT | home mortgage
Customer Accounts | SAV | savings account
Insurance Offerings | SAV | savings account
Individual and Business Loans | SAV | savings account
Customer Accounts | SBL | small business loan
Insurance Offerings | SBL | small business loan
Individual and Business Loans | SBL | small business loan

结果集有24行，这是由product表的8行和product_type表的3行交叉构成的。

虽然交叉连接很少被使用，但其也具有适合使用的情况。

先来看上一章的虚拟表：

    SELECT 'Small Fry' NAME, 0 low_limit, 4999.99 High_limit
    UNION ALL
    SELECT 'Average Joes' NAME, 5000 low_limit, 9999.99 High_limit
    UNION ALL
    SELECT 'Heavy Hitters' NAME, 10000 low_limit, 9999999.99 High_limit;

虽然在上一章中，这样生成虚拟表的方式很适合，但是这种策略在虚拟构建一个大型表时就不能很好地起作用了。

例如，我们现在打算创建一个查询，为2008年每一天生成一行，如果我们采取上述策略，就需要这样做：

    SELECT '2008-01-01' dt
    UNION ALL
    SELECT '2008-01-02' dt
    UNION ALL
    SELECT '2008-01-03' dt
    UNION ALL
    ...
    SELECT '2008-12-29' dt
    UNION ALL
    SELECT '2008-12-30' dt
    UNION ALL
    SELECT '2008-12-31' dt;

将这个366行的查询结果合并在一起过于繁琐，因此需要其他策略。

我们可以这样：先通过单列生成400行的表，此单列包含0~399中的某个数字，然后将这个天数加上2008年1月1日，最后排除日期超过2008年12月31日的数据。

示例：

    SELECT ones.num + tens.num + hundreds.num
    FROM
    (
        SELECT 0 num UNION ALL
        SELECT 1 num UNION ALL
        SELECT 2 num UNION ALL
        SELECT 3 num UNION ALL
        SELECT 4 num UNION ALL
        SELECT 5 num UNION ALL
        SELECT 6 num UNION ALL
        SELECT 7 num UNION ALL
        SELECT 8 num UNION ALL
        SELECT 9 num 
    ) ones 
    CROSS JOIN 
    (
        SELECT 0 num UNION ALL
        SELECT 10 num UNION ALL
        SELECT 20 num UNION ALL
        SELECT 30 num UNION ALL
        SELECT 40 num UNION ALL
        SELECT 50 num UNION ALL
        SELECT 60 num UNION ALL
        SELECT 70 num UNION ALL
        SELECT 80 num UNION ALL
        SELECT 90 num 
    ) tens
    CROSS JOIN 
    (
        SELECT 0 num UNION ALL
        SELECT 100 num UNION ALL
        SELECT 200 num UNION ALL
        SELECT 300 num 
    ) hundreds
    ORDER BY 1;

返回数据：

<table>
    <tr>
        <td>ones.num + tens.num + hundreds.num</td>
    </tr>
    <tr>
        <td>0</td>
    </tr>
    <tr>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
    </tr>
    <tr>
        <td>...</td>
    </tr>
    <tr>
        <td>398</td>
    </tr>
    <tr>
        <td>399</td>
    </tr>
</table>

如结果所示，如果生成{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}、{0, 10, 20, 30, 40, 50, 60, 70, 80, 90}和{0, 100, 200, 300}这三个集合的笛卡尔积，并将这三列相加，那么就能得到包含0~399所有数字的400行结果集。

接下来将数字集转换为日期集，并添加过滤条件：

    SELECT DATE_ADD('2008-01-01', INTERVAL (ones.num + tens.num + hundreds.num) DAY) dt
    FROM
    (
        SELECT 0 num UNION ALL
        SELECT 1 num UNION ALL
        SELECT 2 num UNION ALL
        SELECT 3 num UNION ALL
        SELECT 4 num UNION ALL
        SELECT 5 num UNION ALL
        SELECT 6 num UNION ALL
        SELECT 7 num UNION ALL
        SELECT 8 num UNION ALL
        SELECT 9 num 
    ) ones 
    CROSS JOIN 
    (
        SELECT 0 num UNION ALL
        SELECT 10 num UNION ALL
        SELECT 20 num UNION ALL
        SELECT 30 num UNION ALL
        SELECT 40 num UNION ALL
        SELECT 50 num UNION ALL
        SELECT 60 num UNION ALL
        SELECT 70 num UNION ALL
        SELECT 80 num UNION ALL
        SELECT 90 num 
    ) tens
    CROSS JOIN 
    (
        SELECT 0 num UNION ALL
        SELECT 100 num UNION ALL
        SELECT 200 num UNION ALL
        SELECT 300 num 
    ) hundreds
    WHERE DATE_ADD('2008-01-01', INTERVAL (ones.num + tens.num + hundreds.num) DAY) < '2009-01-01'
    ORDER BY 1;

返回数据：

<table>
    <tr>
        <td>dt</td>
    </tr>
    <tr>
        <td>2008-01-01</td>
    </tr>
    <tr>
        <td>2008-01-02</td>
    </tr>
    <tr>
        <td>2008-01-03</td>
    </tr>
    <tr>
        <td>...</td>
    </tr>
    <tr>
        <td>2008-12-30</td>
    </tr>
    <tr>
        <td>2008-12-31</td>
    </tr>
</table>

这个方法的好处在于无需人工介入，结果集会自动包含额外的闰日。

使用上述结果集：查询2008年每一日、当天的银行交易数量

    SELECT days.dt, COUNT(t.txn_id)
    FROM TRANSACTION t 
    RIGHT OUTER JOIN 
    (
        SELECT DATE_ADD('2008-01-01', INTERVAL (ones.num + tens.num + hundreds.num) DAY) dt
        FROM
        (
            SELECT 0 num UNION ALL
            SELECT 1 num UNION ALL
            SELECT 2 num UNION ALL
            SELECT 3 num UNION ALL
            SELECT 4 num UNION ALL
            SELECT 5 num UNION ALL
            SELECT 6 num UNION ALL
            SELECT 7 num UNION ALL
            SELECT 8 num UNION ALL
            SELECT 9 num 
        ) ones 
        CROSS JOIN 
        (
            SELECT 0 num UNION ALL
            SELECT 10 num UNION ALL
            SELECT 20 num UNION ALL
            SELECT 30 num UNION ALL
            SELECT 40 num UNION ALL
            SELECT 50 num UNION ALL
            SELECT 60 num UNION ALL
            SELECT 70 num UNION ALL
            SELECT 80 num UNION ALL
            SELECT 90 num 
        ) tens
        CROSS JOIN 
        (
            SELECT 0 num UNION ALL
            SELECT 100 num UNION ALL
            SELECT 200 num UNION ALL
            SELECT 300 num 
        ) hundreds
        WHERE DATE_ADD('2008-01-01', INTERVAL (ones.num + tens.num + hundreds.num) DAY) < '2009-01-01'
    ) days
    ON days.dt = t.txn_date
    GROUP BY days.dt
    ORDER BY 1;

返回数据：*由于transcation表中没有2008年发生交易的记录，所以COUNT()函数值均为0*

dt | count(t.txn_id)
-----|-----
2008-01-01 | 0
2008-01-02 | 0
2008-01-03 | 0
... | ...
2008-12-30 | 0
2008-12-31 | 0

### 10.3 自然连接

自然链接是指依赖多表交叉时的相同列名来推断正确的连接条件。

这个过程需要我们自己指定相关的表，而让服务器去决定需要什么样的连接条件

示例：

    SELECT a.account_id, a.cust_id, c.cust_type_cd, c.fed_id
    FROM account a 
    NATURAL JOIN customer c;

返回数据：

account_id | cust_id | cust_type_cd | fed_id
-----|-----|-----|-----
1 | 1 | I | 111-11-1111
2 | 1 | I | 111-11-1111
3 | 1 | I | 111-11-1111
4 | 2 | I | 222-22-2222
5 | 2 | I | 222-22-2222
7 | 3 | I | 333-33-3333
8 | 3 | I | 333-33-3333
10 | 4 | I | 444-44-4444
11 | 4 | I | 444-44-4444
12 | 4 | I | 444-44-4444
13 | 5 | I | 555-55-5555
14 | 6 | I | 666-66-6666
15 | 6 | I | 666-66-6666
17 | 7 | I | 777-77-7777
18 | 8 | I | 888-88-8888
19 | 8 | I | 888-88-8888
21 | 9 | I | 999-99-9999
22 | 9 | I | 999-99-9999
23 | 9 | I | 999-99-9999
24 | 10 | B | 04-1111111
25 | 10 | B | 04-1111111
27 | 11 | B | 04-2222222
28 | 12 | B | 04-3333333
29 | 13 | B | 04-4444444

由于指定了自然连接，并且account表包含一个名为cust_id的列，它是customer表的外键，也是customer表的主键，因此服务器检查表的定义并给两个表添加了连接条件：`a.cust_id=c.cust_id`。

但是，如果交叉表没有相同名称的列，就会查询出错误的结果。

示例：

    SELECT a.account_id, a.cust_id, a.open_branch_id, b.name
    FROM account a
    NATURAL JOIN branch b;

返回数据：

account_id | cust_id | open_branch_id | name
-----|-----|-----|-----
1 | 1 | 2 | Headquarters
1 | 1 | 2 | Woburn Branch
1 | 1 | 2 | Quincy Branch
1 | 1 | 2 | So. NH Branch
... | ... | ... | ...
28 | 12 | 4 | So. NH Branch
29 | 13 | 3 | Headquarters
29 | 13 | 3 | Woburn Branch
29 | 13 | 3 | Quincy Branch
29 | 13 | 3 | So. NH Branch

上述查询一共会返回96行而不是24行。这是因为虽然account表有来自于branch表的外键，但是在account表中命名为open_branch_id而不是branch_id。所以服务器找不到两个相同名字的列而不能生成连接条件，只能进行交叉连接。

**应该避免使用自然链接。**

## 第十一章 条件逻辑

>
本章讨论如何写一个语句使之随着遇到的数据的不同而采取不同的执行方式。

### 11.1 什么是条件逻辑

简单地说，条件逻辑是执行程序时从多个路径中选取其一的能力。

例如，查询客户信息时，可能希望依据客户类型决定从individual表中检索fname/lname列或者从business表中检索name列。

使用外连接：

    SELECT c.cust_id, 
           c.fed_id, 
           c.cust_type_cd,
           CONCAT(i.fname, ' ', i.lname) indiv_name,
           b.name business_name
    FROM customer c
    LEFT OUTER JOIN individual i
    USING(cust_id)
    LEFT OUTER JOIN business b
    USING(cust_id)
    ORDER BY 1;

返回数据：

cust_id | fed_id | cust_type_cd | indiv_name | business_name
-----|-----|-----|-----|-----
1 | 111-11-1111 | I | James Hadley | NULL
2 | 222-22-2222 | I | Susan Tingley | NULL
3 | 333-33-3333 | I | Frank Tucker | NULL
4 | 444-44-4444 | I | John Hayward | NULL
5 | 555-55-5555 | I | Charles Frasier | NULL
6 | 666-66-6666 | I | John Spencer | NULL
7 | 777-77-7777 | I | Margaret Young | NULL
8 | 888-88-8888 | I | Louis Blake | NULL
9 | 999-99-9999 | I | Richard Farley | NULL
10 | 04-1111111 | B | NULL | Chilton Engineering
11 | 04-2222222 | B | NULL | Northeast Cooling Inc.
12 | 04-3333333 | B | NULL | Superior Auto Body
13 | 04-4444444 | B | NULL | AAA Insurance Inc.

得到结果集后，我们可以查看cust_type_cd列的值，然后决定使用indiv_name还是business_name。

另一种方法，通过CASE表达式使用条件逻辑决定客户类型，进而返回恰当的字符串：

    SELECT c.cust_id, c.fed_id, c.cust_type_cd,
    CASE
        WHEN c.cust_type_cd = 'I' THEN CONCAT(i.fname, ' ', i.lname)
        WHEN c.cust_type_cd = 'B' THEN b.name
        ELSE 'Unknown'
    END NAME
    FROM customer c
    LEFT OUTER JOIN individual i
    USING(cust_id)
    LEFT OUTER JOIN business b
    USING(cust_id)
    ORDER BY 1;

返回数据：

cust_id | fed_id | cust_type_cd | name
-----|-----|-----|-----
1 | 111-11-1111 | I | James Hadley
2 | 222-22-2222 | I | Susan Tingley
3 | 333-33-3333 | I | Frank Tucker
4 | 444-44-4444 | I | John Hayward
5 | 555-55-5555 | I | Charles Frasier
6 | 666-66-6666 | I | John Spencer
7 | 777-77-7777 | I | Margaret Young
8 | 888-88-8888 | I | Louis Blake
9 | 999-99-9999 | I | Richard Farley
10 | 04-1111111 | B | Chilton Engineering
11 | 04-2222222 | B | Northeast Cooling Inc.
12 | 04-3333333 | B | Superior Auto Body
13 | 04-4444444 | B | AAA Insurance Inc.

这个版本的查询只返回CASE表达式生成的单个name列。CASE表达式首先检查cust_type_cd列的值，然后依据该值决定返回个人姓名还是企业名称。

### 11.2 CASE表达式

大部分编程语言都存在if-else语句，所有的主流服务器也都包含模拟此功能的内置函数：

- Oracle：decode()函数
- MySQL：if()函数
- SQL Server：coalesce()函数

CASE表达式是SQL标准的一部分，Oracle、SQL Server、MySQL、Sybase、PostgreSQL等其他数据库均对其进行了实现。
CASE表达式已经内置于SQL语法，可用于SELECT、INSERT、UPDATE和DELETE语句。

#### 11.2.1 查找型CASE函数

前述的CASE表达式是一种查找型表达式的范例，其语法如下：

    CASE
        WHEN C1 THEN E1
        WHEN C2 THEN E2
        ...
        WHEN CN THEN EN
        [ELSE ED]
    END

C1、C2...CN表示条件，E1、E2...EN代表CASE表达式返回的表达式。如果WHEN子句中条件为TRUE，那么CASE表达式返回相应的表达式。符号ED表示默认表达式，也就是当没有任何WHEN子句返回TRUE时将返回的表达式。*ELSE子句是可选的。*各种WHEN子句返回的表达式的计算结果必须类型相同。

示例：使用CASE表达式对TELLER进行分级

    CASE
        WHEN employee.title = 'Head Teller'
            THEN 'Head Teller'
        WHEN employee.title = 'Teller' AND YEAR(employee.start_date) > 2007
            THEN 'Teller Trainee'
        WHEN employee.title = 'Teller' AND YEAR(employee.start_date) < 2006
            THEN 'Experienced Teller'
        WHEN employee.title = 'Teller'
            THEN 'Teller' -- 2006~2007年之间入职的Teller
        ELSE 'Non-Teller'
    END


CASE表达式可以返回任意类型的表达式，甚至包括子查询。

示例：使用子查询代替外连接

    SELECT c.cust_id, c.fed_id, c.cust_type_cd,
    CASE 
        WHEN c.cust_type_cd = 'I' THEN
        (
            SELECT CONCAT(i.fname, ' ', i.lname)
            FROM individual i
            WHERE i.cust_id = c.cust_id
        )
        WHEN c.cust_type_cd = 'B' THEN
        (
            SELECT b.name
            FROM business b
            WHERE b.cust_id = c.cust_id
        )
        ELSE 'Unknown'
    END NAME
    FROM customer c
    ORDER BY 1;

返回数据：

cust_id | fed_id | cust_type_cd | name
-----|-----|-----|-----
1 | 111-11-1111 | I | James Hadley
2 | 222-22-2222 | I | Susan Tingley
3 | 333-33-3333 | I | Frank Tucker
4 | 444-44-4444 | I | John Hayward
5 | 555-55-5555 | I | Charles Frasier
6 | 666-66-6666 | I | John Spencer
7 | 777-77-7777 | I | Margaret Young
8 | 888-88-8888 | I | Louis Blake
9 | 999-99-9999 | I | Richard Farley
10 | 04-1111111 | B | Chilton Engineering
11 | 04-2222222 | B | Northeast Cooling Inc.
12 | 04-3333333 | B | Superior Auto Body
13 | 04-4444444 | B | AAA Insurance Inc.

这个版本的查询只包含FROM子句中的customer表，并且使用关联子查询为每个客户检索合适的名字。
相较于外连接版本，这个版本更好。因为使用这个版本时，服务器只在需要时才从individual和business表中读取数据，而不会总是连接所有的表。

#### 11.2.2 简单CASE表达式

简单CASE表达式和查找型CASE表达式非常相似，但灵活性不够。

语法如下：

    CASE V0
        WHEN V1 THEN E1
        WHEN V2 THEN E2
        ...
        WHEN V3 THEN EN
        [ELSE ED]
    END

这里V0代表一个值，符号V1、V2...VN代表要与V0比较的值，符号E1、E2...EN代表CASE表达式要返回的表达式，ED代表V1、V2...VN没有一个值匹配V0时返回的默认值。

示例：

    CASE customer.cust_type_cd
        WHEN 'I' THEN
        (
            SELECT CONCAT(i.fname, ' ', i.lname)
            FROM individual i
            WHERE i.cust_id = c.cust_id
        )
        WHEN 'B' THEN
        (
            SELECT b.name
            FROM business b
            WHERE b.cust_id = c.cust_id
        )
        ELSE 'Unknown'
    END 

上述的简单CASE表达式相当于下面的查找型CASE表达式：

    CASE 
        WHEN c.cust_type_cd = 'I' THEN
        (
            SELECT CONCAT(i.fname, ' ', i.lname)
            FROM individual i
            WHERE i.cust_id = c.cust_id
        )
        WHEN c.cust_type_cd = 'B' THEN
        (
            SELECT b.name
            FROM business b
            WHERE b.cust_id = c.cust_id
        )
        ELSE 'Unknown'
    END 

简单CASE表达式没有查找型CASE表达式强大，因为简单CASE表达式自动构建了等式条件，而不是读者自己指定条件。

### 11.3 CASE表达式范例

下面提供一些范例说明SQL语句中条件逻辑的用途。

#### 11.3.1 结果集变换

示例，有以下查询：返回2000年到2005年每年的开户数量

    SELECT EXTRACT(YEAR FROM open_date) YEAR, COUNT(*) how_many
    FROM account
    WHERE open_date BETWEEN '1999-12-31' AND '2006-01-01'
    GROUP BY YEAR;

返回数据：

year | how_many
-----|-----
2000 | 3
2001 | 4
2002 | 5
2003 | 3
2004 | 9

现在要求返回一个单行6列的结果（每年一列），这时我们需要创建6列，并在每列中只对与所求年份相关的行求和。

    SELECT 
    SUM(CASE 
            WHEN EXTRACT(YEAR FROM open_date) = 2000 THEN 1 ELSE 0
        END) year_2000,
    SUM(CASE 
            WHEN EXTRACT(YEAR FROM open_date) = 2001 THEN 1 ELSE 0
        END) year_2001,
    SUM(CASE 
            WHEN EXTRACT(YEAR FROM open_date) = 2002 THEN 1 ELSE 0
        END) year_2002,
    SUM(CASE 
            WHEN EXTRACT(YEAR FROM open_date) = 2003 THEN 1 ELSE 0
        END) year_2003,
    SUM(CASE 
            WHEN EXTRACT(YEAR FROM open_date) = 2004 THEN 1 ELSE 0
        END) year_2004,
    SUM(CASE 
            WHEN EXTRACT(YEAR FROM open_date) = 2005 THEN 1 ELSE 0
        END) year_2005
    FROM account
    WHERE open_date BETWEEN '1999-12-31' AND '2006-01-01';

返回数据：

year_2000 | year_2001 | year_2002 | year_2003 | year_2004 | year_2005
-----|-----|-----|-----|-----|-----
3 | 4 | 5 | 3 | 9 | 0

#### 11.3.2 选择性聚合

下面的例子用于查找哪些帐户余额与transaction表中的原始数据不相符的账户。

以下是部分解决方案：

    SELECT CONCAT('ALERT! : Account #', a.account_id, ' Has Incorrect Balance!')
    FROM account a
    WHERE (a.avail_balance, a.pending_balance) <>
    (
        SELECT SUM(<expression to generate available balance>),
               SUM(<expression to generate pending balance>)
        FROM transaction t
        WHERE t.account_id = a.account_id
    );

下面解决两个SUM()函数内的表达式问题。

查询使用关联子查询对transaction表统计指定账户的个人交易。统计交易时需要考虑下面两个问题：

- 由于交易账户总是整的，所以需要查看交易类型是借款还是存款，借款则应该翻转标志（乘以-1）。
- 如果funds_avail_date列中的日期大于当前日期，交易应该被加到待收月总和，而不是加到可用余额总和。

有些交易需要被排除在可用余额之外，而所有交易都应该被包含在待收余额之内，这就使后者称为两个计算中较简单的一个。

下面的CASE表达式计算待收余额：

    CASE
        WHEN transaction.txn_type_cd = 'DBT'
            THEN transaction.amount * -1
        ELSE transaction.amount
    END

可用余额计算时也采用同样的逻辑，不过只有哪些可用的交易才能被包含在内。

下面的CASE表达式计算可用余额：

    CASE
        WHEN transaction.funds_avail_date > CURRENT_TIMESTAMP()
            THEN 0
        WHEN transaction.txn_type_cd = 'DBT'
            THEN transaction.amount * -1
        ELSE transaction.amount
    END

有了第一个WHEN子句的存在，那些不可用的资金（例如，尚未兑现的支票）对于总和的贡献为0.

最终查询：

    SELECT CONCAT('ALERT! : Account #', a.account_id, ' Has Incorrect Balance!')
    FROM account a
    WHERE (a.avail_balance, a.pending_balance) <>
    (
        SELECT 
        SUM(CASE
                WHEN t.funds_avail_date > CURRENT_TIMESTAMP()
                    THEN 0
                WHEN t.txn_type_cd = 'DBT'
                    THEN t.amount * -1
                ELSE t.amount
            END),
        SUM(CASE
                WHEN t.txn_type_cd = 'DBT'
                    THEN t.amount * -1
                ELSE t.amount
            END)
        FROM TRANSACTION t
        WHERE t.account_id = a.account_id
    );

#### 11.3.3 存在性查询

有时候我们只关心两个实体之间是否存在某种关系，而并不关心数量的多少。例如，查询某个客户是否有支票账户或者储蓄账户，但是并不关心每个类型的账户是否多于一个。

下面的查询使用多个CASE表达式生成两个列，一列显示客户是否有支票账户，另一列显示他是否有储蓄账户。

    SELECT c.cust_id, c.fed_id, c.cust_type_cd,
    CASE
        WHEN EXISTS
        (
            SELECT 1 FROM account a
            WHERE a.cust_id = c.cust_id
            AND a.product_cd = 'CHK'
        ) THEN 'Y'
        ELSE 'N'
    END has_checking,
    CASE
        WHEN EXISTS
        (
            SELECT 1 FROM account a
            WHERE a.cust_id = c.cust_id
            AND a.product_cd = 'SAV'
        ) THEN 'Y'
        ELSE 'N'
    END has_savings
    FROM customer c;

返回数据：

cust_id | fed_id | cust_type_cd | has_checking | has_savings
-----|-----|-----|-----|-----
1 | 111-11-1111 | I | Y | Y
2 | 222-22-2222 | I | Y | Y
3 | 333-33-3333 | I | Y | N
4 | 444-44-4444 | I | Y | Y
5 | 555-55-5555 | I | Y | N
6 | 666-66-6666 | I | Y | N
7 | 777-77-7777 | I | N | N
8 | 888-88-8888 | I | Y | Y
9 | 999-99-9999 | I | Y | N
10 | 04-1111111 | B | Y | N
11 | 04-2222222 | B | N | N
12 | 04-3333333 | B | Y | N
13 | 04-4444444 | B | N | N

这里使用了EXISTS运算符，因此只要客户有至少一个所需的账户，那么条件就为TRUE。

下面的查询使用简单CASE表达式计算每个客户的账户数目，然后返回NONE、1、2、3+。

    SELECT c.cust_id, c.fed_id, c.cust_type_cd,
    CASE
    (   
        SELECT COUNT(*) FROM account a
        WHERE a.cust_id = c.cust_id
    ) 
        WHEN 0 THEN 'NONE'
        WHEN 1 THEN '1'
        WHEN 2 THEN '2'
        ELSE '3+'
    END num_accounts
    FROM customer c;

返回数据：

cust_id | fed_id | cust_type_cd | num_accounts
-----|-----|-----|-----
1 | 111-11-1111 | I | 3+
2 | 222-22-2222 | I | 2
3 | 333-33-3333 | I | 2
4 | 444-44-4444 | I | 3+
5 | 555-55-5555 | I | 1
6 | 666-66-6666 | I | 2
7 | 777-77-7777 | I | 1
8 | 888-88-8888 | I | 2
9 | 999-99-9999 | I | 3+
10 | 04-1111111 | B | 2
11 | 04-2222222 | B | 1
12 | 04-3333333 | B | 1
13 | 04-4444444 | B | 1

#### 11.3.4 除零错误

有些数据库（例如Oracle）在遇到分母为0的情况时将抛出一个错误，而MySQL只是简单地将结果置为NULL。

示例：

    SELECT 1 / 0;

返回数据：

<table>
    <tr>
        <td>1 / 0</td>
    </tr>
    <tr>
        <td>NULL</td>
    </tr>
</table>

为了防止上述情况，应当将所有的分母都包装在条件逻辑中。

示例：

    SELECT a.cust_id, a.product_cd, a.avail_balance / 
    CASE
        WHEN prod_tots.tot_balance = 0 THEN 1
        ELSE prod_tots.tot_balance
    END percent_of_total
    FROM account a 
    INNER JOIN 
    (
        SELECT a.product_cd, SUM(a.avail_balance) tot_balance
        FROM account a
        GROUP BY a.product_cd
    ) prod_tots
    ON a.product_cd = prod_tots.product_cd;

返回数据：

cust_id | product_cd | percent_of_total
----- | ------ | -----
1 | CHK | 0.014488
1 | SAV | 0.269431
1 | CD | 0.153846
2 | CHK | 0.030928
2 | SAV | 0.107773
3 | CHK | 0.014488
3 | MM | 0.129802
4 | CHK | 0.007316
4 | SAV | 0.413723
4 | MM | 0.321915
5 | CHK | 0.030654
6 | CHK | 0.001676
6 | CD | 0.512821
7 | CD | 0.256410
8 | CHK | 0.047764
8 | SAV | 0.209073
9 | CHK | 0.001721
9 | MM | 0.548282
9 | CD | 0.076923
10 | CHK | 0.322911
10 | BUS | 0.000000
11 | BUS | 1.000000
12 | CHK | 0.528052
13 | SBL | 1.000000

#### 11.3.5 有条件更新

可以使用CASE表达式设定更新条件。

示例：

    UPDATE account 
    SET last_activity_date = CURRENT_TIMESTAMP(),
        pending_balance = pending_balance + 
        (
            SELECT t.amount *
            CASE t.txn_type_cd -- NO.1
                WHEN 'DBT' THEN -1
                ELSE 1
            END
            FROM TRANSACTION t
            WHERE t.txn_id = 999
        ),
        avail_balance = avail_balance + 
        (
            SELECT 
            CASE -- NO.2
                WHEN t.funds_avail_date > CURRENT_TIMESTAMP() THEN 0
                ELSE t.amount * 
                    CASE t.txn_type_cd -- NO.3
                    WHEN 'DBT' THEN -1
                    ELSE 1
                    END
            END
            FROM TRANSACTION t
            WHERE t.txn_id = 999    
        )
        WHERE account.account_id = 
        (
            SELECT t.account_id
            FROM TRANSACTION t
            WHERE t.txn_id = 999
        );

这个语句共有3个CASE表达式，第一个和第三个用于对交易账户的借款额使用翻转符号，第二个表达式用于检查资金的可用性。如果日期是未来，则只对可用余额加0，否则，应该加上这个交易额。

#### 11.3.6 NULL值处理

NULL值是在某列未知时存储到表中的值，不过在检索时显示NULL值或者NULL值参与表达式不总是合适的。

我们可以在值为NULL时使用CASE表达式来替换字符串：

    SELECT emp_id, fname, lname,
    CASE
        WHEN title IS NULL THEN 'Unknown'
        ELSE title
    END title
    FROM employee;

在执行计算时，NULL值通常会导致一个NULL结果。可以使用CASE表达式将NULL转换为一个数字（通常是0或1），从而使计算得到非NULL的结果。

假设计算包括account.avail_balance列，对于那些刚刚建立但尚存款的账户，就可以使用0代替（如果是加法或减法），或者用1代替（如果是乘法或除法）。

示例：

    SELECT <some calculation> + 
    CASE
        WHEN avail_balance IS NULL THEN 0
        ELSE avail_balance
    END
    + <rest of calculation>
    ...

**如果一个数字列允许包含NULL值，那么在任何包含此列的计算中，为确保可以产生有用的结果，使用条件逻辑是很有用的。**

## 第十二章 事务

>
本章讨论多个SQL语句同时执行的必要性和所需的基础设施。

### 12.1 多用户数据库

数据库管理系统不但允许单个用户查询或者修改数据，而且可以多人同时操作。如果每个人都只进行查询，那么数据库服务器就只有很少的问题需要处理。但如果一些用户正在添加或者修改数据，那么服务器就必须做更多的簿记。

#### 12.1.1 锁

锁是数据库服务器用来控制数据资源被并行使用的一种机制。当数据库的一些内容被锁定时，任何打算修改（或者读取）这个数据的用户必须等到锁被释放。

大部分数据库使用下面两种策略之一：

- 数据库的写操作必须向服务器申请并获得写锁才能修改数据，而读操作必须申请和获得读锁才能查询数据。多用户可以同时读取数据，而一个表（或其他部分）一次只能分配一个写锁，并且拒绝读请求直至写锁释放。
- 数据库的写操作必须向服务器申请并获得写锁才能修改数据，而读操作不需要任何类型的锁就可以查询数据。另一方面，服务器要保证从查询开始到结束读操作看到一个一致的数据视图（即使其他用户修改，数据看上去也要相同）。这个方法被称为版本控制。

两种方法各有利弊：

- 第一种方法在有较多的并行读请求和写请求时等待时间过长
- 第二种方法在修改数据时如果存在长期运行的查询也会发生问题

SQL Server采取第一种方法，Oracle使用第二种方法，MySQL两种方法均可使用（取决于读者对存储引擎的选择）。

#### 12.1.2 锁的粒度

决定如何锁定一个资源时也可以采用一些不同的策略。服务器可能在3各不同级别之一应用锁，或者称为粒度：

- 表锁：阻止多用户同时修改同一个表的数据。
- 页锁：阻止多用户同时修改某表中同一页（一页通常是一段2~16KB的内存空间）的数据。
- 行锁：：阻止多用户同时修改某表中同一行的数据。

这些方法各有利弊。表锁需要较少的簿记就可以锁定整个表，但是用户增多时它会迅速产生不可接受的等待实践。行锁需要更多的簿记，但是只要各个用户的兴趣在不同的行，它就能允许多人修改同一个表。

SQL Server使用表锁、页锁和行锁，Oracle只有行锁，MySQL采用表锁、页锁或行锁（取决于存储引擎的选择）。某些情况下SQL Server会将锁从行锁升级至页锁，再从页锁升级至表锁。

### 12.2 什么是事务

简单来说，事务是一种将多条SQL语句聚集到一起，并且能够实现要么所有语句都执行，要么一个都不执行（这个属性称为原子性）。

示例：从储蓄账户转$500到支票账户

伪代码：

    START TRANSACTION;
    /*
        withdraw money from first account, 
        making sure balance is sufficient
    */
    UPDATE account SET avail_balance - 500
    WHERE account_id = 9988 AND avail_balance > 500;

    IF <exactly one row was updated by the previous statement>
    /* deposit money into second account */
    THEN 
        UPDATE account SET avail_balance + 500
        WHERE account_id = 9989;

        IF <exactly one row was updated by the previous statement>
        THEN
            /* everything worked, make the changes parmanent */ 
            COMMIT;
        ELSE
            /* something went wrong, undo all changes in this transaction */
            ROLLBACK;
        END IF;
    ELSE
        /* insufficient funds, or error encountered during update */
        ROLLBACK;
    END IF;

上述代码从启动事务开始，然后视图从储蓄账户转出$500到支票账户。如果一切顺利，事务将被提交；如果出现了错误，事务将回滚，这意味着将撤销子事务开始的所有数据变化。

通过使用事务，程序可以确保着$500要么在储蓄账户，要么在支票账户，而不会发生什么意外。

不管事务提交还是回滚，执行时获得的资源在事务完成后都会被释放。

当然，如果程序设法完成两个UPDATE语句后，还没有执行COMMIT或ROLLBACK命令，服务器就突然发生了宕机，那么事务会在服务器重新上线后被回滚。此外，如果程序完成了事务，并发出了COMMIT指令，但在还没有将变化持久化到永久存储区时服务器发生宕机，那么服务器重新上线后数据库服务器必须重新应用事务的变化。

#### 12.2.1 启动事务

数据库服务器以下面两种方式之一创建事务：

- 一个活跃事务总是和数据库会话相联系，所以没有必要，也没有什么方法能够显式地启动一个事务。当前事务结束时，服务器自动为会话启动一个新的事物。
- 如果不显式地启动一个会话，单个的SQL语句会被独立于其他语句自动提交。启动一个事务之前需要先提交一个命令。

Oracle采用了第一种方式，SQL Server和MySQL采用了第二种方式。

SQL:20030标准包含了`START TRANSACTION`指令，它用来显式地启动一个事物。MySQL遵守了这个标准（SQL Server中使用`BEGIN TRANSACTION`）。对于MySQL和SQL Server来说，默认情况下，用户一直处于自动提交模式，直到显式地启动一个事务，这意味着单个语句会被服务器自动提交。因此用户可以自己决定进入事务模式并提交启动事务命令或者只是简单地让服务器提交单个语句。

MySQL下关闭自动提交模式：
    
    SET autocommit = 0;

一旦离开了自动提交模式，所有的SQL命令都会发生在同一个事务的范围里，并且必须显式地对事务进行提交或者回滚。

`START TRANSACTION`与autocommit = 0的关系：

- 不管autocommit是1还是0，`START TRANSACTION`后，只有当提交COMMIT后数据才会生效，而ROLLBACK就会回滚。
- 当autocommit为0时，不管有没有`START TRANSACTION`，
只有当提交COMMIT后数据才会生效，而ROLLBACK就会回滚。

#### 12.2.2 结束事务

一旦事务启动，不管是通过`START TRANSACTION`命令显式地启动还是由数据库服务器隐式地启动，为了持久化数据变化，都必须显式地结束事务。

如果希望将变化持久化，可以使用COMMIT命令，持久化后会释放事务中使用的任何资源。
如果希望撤销自事务启动时所发生的一切变化，可以使用ROLLBACK命令，撤销后同样会释放事务中使用的任何资源。

除了使用COMMIT或ROLLBACK命令，结束事务还可以由其他情景触发，要么作为活动的间接结果，要么作为意外结果：

- 服务器宕机，这种情况下，服务器重启时事务将会被自动回滚。
- 提交一个SQL模式语句，例如`ALTER TABLE`，这将引起当前事务的提交和一个新事务的启动。
- 提交一个`START TRANSACTION`命令，将会引起前一个事务提交。
- 如果服务器检测到一个死锁并且去顶当前事务是造成死锁的原因，那么服务器就会提前结束当前事务。这种情况下，事务将会被回滚，同时释放错误信息。

#### 11.2.3 事务保存点

我们可能想要回滚事务，但是并不想撤销所有做过的工作。此时我们可以在事务中创建一个或多个保存点，这样就可以使用它们回滚到事务的特定位置而不必回滚到事务启动的状态。

创建保存点必须要有名字，这样就可以在单个事务中拥有多个保存点。

示例：

    SAVEPOINT my_savepoint;

回滚至特定保存点：

    ROLLBACK TO SAVEPOINT my_savepoint;

使用保存点时需要注意以下两点：

- 创建保存点时，除了名字，什么都没有保存。为了保证事务的持久化，我们必须在最后发出COMMIT命令。
- 如果发出没有保存点的ROLLBACK命令，那么事务中的所有保存点将被忽略，并且撤销整个事务。

## 第十三章 索引和约束

>
本章讨论数据库的两个重要功能：索引和约束。

### 13.1 索引

当向一个表插入一行时，数据库服务器不会视图将数据放到表里任何特定的底方。例如，要向department表增加一行，那么服务器不会依据dept_id列的数字顺序或者name列的字母顺序存放该行。相反，服务器只是简单地将数据存放在文件中下一个可存放的位置。因此，当查询department表时，服务器需要通过检查表的每一行来完成查询。

示例：

    SELECT dept_id, NAME
    FROM department
    WHERE NAME LIKE 'A%';

返回数据：

dept_id | name
-----|-----
3 | Administration

为了寻找所有名字以A开头的部门，服务器必须访问department表中的每一行并检查name列的内容。如果部门名以A开头，就将该行加入结果集。这种类型的访问称为表扫描。

全表扫描对于行数较少的表比较何时，但是当行数非常大的时候，使用全表扫描就可能无法在何时的时间内完成查询。此时就需要索引的帮助。

如同人们使用索引在出版物中查找单词一样，数据库服务器也使用索引定位表中的行。与普通的数据表不同，**索引是一种以特定顺序保存的专用表。**不过，索引并不包含实体中的所有数据，而是那些用于定位表中行的列，以及描述这些行的物理位置的信息。因此，**索引的作用就是便捷化检索表中行和列的子集，而不需要检查表中的每行。**

#### 13.1.1 创建索引

我们可以使用ALTER语句为一个表中的某个字段添加索引。

示例：

    ALTER TABLE department ADD INDEX dept_name_idx (name);

这个语句为department.name列创建了名为dept_name_idx的索引。有了索引后，如果索引有利于改善查询，查询优化器就可以选择索引（假如department表只有3行，那么优化器选择忽略索引而直接读取整个表可能会更合理）。如果表中索引不止一个，那么优化器就必须判断对于特定的SQL语句使用哪个索引最有利。

所有数据库服务器都允许我们查看可用索引。

MySQL使用SHOW命令查看指定表中的所有索引：

    SHOW INDEX FROM department;

返回数据：

Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment
-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----
department | 0 | PRIMARY | 1 | dept_id | A | 2 | NULL | NULL |  | BTREE |  | 
department | 1 | dept_name_idx | 1 | NAME | A | 2 | NULL | NULL |  | BTREE |  | 

或者可以在命令行下使用如下语句：

    -- \G 放到sql语句后，可以使每个字段打印到单独的行。
    SHOW INDEX FROM department \G

返回数据：

    *************************** 1. row ***************************
            Table: department
       Non_unique: 0
         Key_name: PRIMARY
     Seq_in_index: 1
      Column_name: dept_id
        Collation: A
      Cardinality: 2
         Sub_part: NULL
           Packed: NULL
             Null:
       Index_type: BTREE
          Comment:
    Index_comment:
    *************************** 2. row ***************************
            Table: department
       Non_unique: 1
         Key_name: dept_name_idx
     Seq_in_index: 1
      Column_name: NAME
        Collation: A
      Cardinality: 2
         Sub_part: NULL
           Packed: NULL
             Null:
       Index_type: BTREE
          Comment:
    Index_comment:

结果显示department表共有两个索引：

- dept_id列的索引PRIMARY
- name列的索引dept_name_idx

dept_id的PRIMARY索引来自于创建该表时的主键约束。当表被创建时，MySQL会自动为主键列生成索引。所以我们不必为主键创建索引。

当然我们也可以删除索引，删除索引同样使用ALTER命令。

示例：

    ALTER TABLE department DROP INDEX dept_name_idx;

**唯一索引：**

设计数据表时，需要考虑哪些列能够包含重复数据，哪些列不能。例如individual表中可以有两个同名的客户，因为每行的cust_id是不同的，出生日期和fed_id也可以帮助区分这两个人。但是department表理论上不会存在两个相同名字的部门，所以除了dept_id列，name列也可以用于取分两个部门。

由于部门名字是唯一的，所以我们可以对name列添加唯一索引。唯一索引除了能提供常规索引的所有好处外，还可以限制列不能出现重复值。无论是插入一条新的记录还是修改索引列，数据库服务器都会检查唯一索引以判断该值是否已存在于本表的某一行。

为department.name创建唯一索引

    ALTER TABLE department ADD UNIQUE dept_name_idx (NAME);

此时如果视图再添加一个名为Operations的部门，服务器会报错：

    INSERT INTO department VALUES (999, 'Operations');

发生报错：

    Error Code: 1062
    Duplicate entry 'Operations' for key 'dept_name_idx'

**多列索引：**

除了单列索引，我们也可以创建跨越多列的索引。例如如果经常使用姓氏和名字查找雇员，我们就可以为这两列一起创建索引。

示例：

    ALTER TABLE employee ADD INDEX emp_names_index (lname, fname);

**在创建多列索引时需要考虑列的顺序，这样索引才会尽可能的有用。**

#### 13.1.2 索引类型

以下介绍各种服务器采用的不同类型索引。

**B树索引：**

MySQL、SQL Server以及Oracle默认都使用B树索引。所以没有显式地指定索引类型时，索引就是B树索引。

B树索引以树结构组织，它有一个或多个分支节点，分支节点又指向叶节点。分支节点用于遍历树，叶节点则保存真正的值和为止信息。

示例，employee.name的B树索引：

- A-M（分支节点）
    + A-C（叶节点）
        * Barker（叶节点中保存的数据及位置信息）
        * Blake
    + D-F
        * Fleming
        * Fowler
    + G-I
        * Gooding
        * Grossman
        * Hawthorne
    + J-M
        * Jameson
        * Markham
        * Mason
- N-Z
    + N-P
        * Parker
        * Portman
    + Q-S
        * Roberts
        * Smith
    + T-V
        * Tucker
        * Tulman
        * Tyler
    + W-Z
        * Ziegler

当发起一个查询，检索所有姓氏以G开头的雇员时，服务器将首先查找根节点，然后顺着指针前进到姓氏以A-M开始的分支节点，然后服务器会在此分支节点内一次查看直至找到姓氏以G-I开始的叶节点，最后服务器开始读取叶节点中的数据直至遇到一个不以G开头的值（Hawthorne）。

当向employee表中插入、更新、删除数据行时行时，服务器会尽力保持树的平衡，这样就不会出现根节点的某一侧拥有比另一侧多得多的分支节点/叶节点。通过保持树的平衡，服务器不需要遍历多层分支节点就能快速到达叶节点并查找到需要的值。

**位图索引：**

B树索引擅长处理包含许多不同值的列，但当列中的不同值很少时就会变得很难使用。

例如，为了快速检索某一特定类型（支票账户、储蓄账户）的所有账户，我们可以希望为account.product_cd列创建索引。但是由于总共只有8种不同的产品，并且某些产品比其他产品受欢迎的多，所以客户数目的不断增长将会使得B树索引很难继续维持平衡。

对于哪些包含少量值但却占据了大量行的列（低基数列），应该采用不同的索引策略。
为了处理这个问题，Oracle数据库引入了位图索引，他为存储在某列种的每个值生成一个位图。

示例，account.product_cd种数据的位图索引如下：

Value/row|1|2|3|4|5|6|7|8|9|10|11|12|13|14|15|16|17|18|19|20|21|22|23|24
-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----
BUS|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|1|1|0|0
CD|0|0|1|0|0|0|0|0|0|0|0|0|1|1|0|0|0|0|1|0|0|0|0|0
CHK|1|0|0|1|0|1|0|1|0|0|1|1|0|0|1|0|1|0|0|1|0|0|1|0
MM|0|0|0|0|0|0|1|0|0|1|0|0|0|0|0|0|0|1|0|0|0|0|0|0
SAV|0|1|0|0|1|0|0|0|1|0|0|0|0|0|0|1|0|0|0|0|0|0|0|0
SBL|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|1

这个索引包含6个位图，分别对应`account.product`列中的每个值（8个可用产品中有两个没被使用），并且每个位图为account表中的24行都分配了0/1值。

现在加入服务器查找所有货币市场账户（product_cd = 'MM'），那么服务器只需要简单地查找MM位图中所有1的值对应的行就可以了，结果返回7、10和18行。如果查找所有货币市场账户和存储账户（product_cd IN ('MM', 'SAV')），此时服务器对MM和SAV的位图执行或运算，最后返回2、5、7、9、10、16和18行。

**对于低基数而言，位图是很好的索引解决方案，但是当列中存储的值的数目攀升到行数太高（高基数）时，这种索引策略将会失败。**因为此时服务器需要维护太多的位图。例如为主键列创建一个位图索引，此时该列拥有最高的基数（每行都具有不同的值），这意味着此时服务器要维护n*n（n为该表的总行数）个位图。

**文本索引：**

如果数据库中存储文档，那么可能需要允许用户在文档中查找单词或者短语。我们当然不希望每次请求搜索时服务器都打开每个文档，然后扫描需要的文本。为了处理这种情况，MySQL、SQL Server以及Oracle为文档提供了专业的索引和搜索机制。其中MySQL和SQL Server提供全文索引，Oracle提供一个名为Oracle Text的工具集。

#### 13.1.3 如何使用索引

服务器通常首先利用索引快速定位特定表中的行，之后再访问相关表提取用户请求的补充信息。

示例：

    SELECT emp_id, fname, lname 
    FROM employee 
    WHERE emp_id IN (1, 3, 9, 15);

返回数据：

emp_id | fname | lname
-----|-----|-----
1 | Michael | Smith
3 | Robert | Tyler
9 | Jane | Grossman
15 | Frank | Portman

对于本查询来说，服务器首先使用`employee.emp_id`的主键索引定位ID为1、3、9和15的雇员，然后再访问这4行，获取姓氏和名字两列。

不过，如果索引包含满足查询的所有内容，那么服务器就不必访问相关表了。

下面来看查询优化器如何使用不同的索引处理相同的查询，以下查询为指定的客户聚合账户余额：

    SELECT cust_id, SUM(avail_balance) tot_bal
    FROM account 
    WHERE cust_id IN (1, 5, 9, 11)
    GROUP BY 1;

返回数据：

cust_id | tot_bal
-----|-----
1 | 4557.75
5 | 2237.97
9 | 10971.22
11 | 9345.55

使用EXPLAIN语句请求服务器显示查询的执行计划：

    EXPLAIN SELECT cust_id, SUM(avail_balance) tot_bal
    FROM account
    WHERE cust_id IN (1, 5, 9, 11)
    GROUP BY 1 \G

返回结果：

    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: account
       partitions: NULL
             type: index
    possible_keys: fk_a_cust_id
              key: fk_a_cust_id
          key_len: 4
              ref: NULL
             rows: 24
         filtered: 33.33
            Extra: Using where

这个执行计划告诉我们以下内容：

- 使用索引fk_a_cust_id查找account表中满足WHERE子句的行。
- 读取索引后服务器预计会读取account表的所有24行以聚合可用余额的数据。这是因为它不知道除了ID为1、5、9和11的客户外，表中还存在其他客户。

索引fk_a_cust_id是由服务器自动生成的有一个索引，但是这次自动生成的原因是外键约束而不是主键约束。fk_a_cust_id是`account_cust_id`列的索引，因此服务器先使用索引定位account表中ID为1、5、9和11的客户，然后访问这些行，再实现检索和聚合可用余额数据。

下面为cust_id和avail_balance两列添加新的索引acc_bal_idx：

    ALTER TABLE account ADD INDEX acc_bal_index (cust_id, avail_balance);

有了这个索引后，再次查询优化器是如何计划查询的：

    EXPLAIN SELECT cust_id, SUM(avail_balance) tot_bal
    FROM account
    WHERE cust_id IN (1, 5, 9, 11)
    GROUP BY 1 \G

返回数据：

    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: account
       partitions: NULL
             type: range
    possible_keys: acc_bal_index
              key: acc_bal_index
          key_len: 4
              ref: NULL
             rows: 8
         filtered: 100.00
            Extra: Using where; Using index


比较上述两个执行计划，可以得到以下区别：

- 优化器使用了新的索引acc_bal_idx，而不是fk_a_cust_id。
- 优化器预期只需要执行8行，而不是24行

此时不再需要对account表进行查询就可满足查询结果，因为索引已经包含了所需的所有信息。换句话说，如果索引包含查询需要的所有列，那么服务器就可以把索引当作表来使用。

#### 13.1.4 索引的不足

既然索引如此有用，为什么不索引一切呢？

每个索引事实上都是一个表。因此，每次对表添加或删除时，表中的所有索引必须被更改；当更新行时，收到影响的列的任何索引也必须被更改。因此索引越多，服务器就需要做越多的工作来保持所有模式对象最新。这会加重服务器维护表的负担。与此同时，索引需要磁盘空间。

因此对于索引的最佳策略是：**仅当出现清晰需求时才添加索引。**如果有特殊目的需要索引，例如月度例行维护程序，那么可以添加索引，运行程序，然后删除索引。同样地，当对表进行一次性大规模的添加、修改或删除操作前，可以先删除索引，执行操作，最后再重建索引。

通常来说，我们应该**避免使用太多和太少的索引。**如果不能确定到底需要多少索引，可以将下面内容作为默认策略：

- 确保所有主键列被索引。针对多列主键，考虑为主键列的子集构建附加索引，或者以与主键约束定义不同的顺序为所有主键列另外生成索引。
- 为所有被外键约束引用的列创建索引。服务器再准备删除父行时会查找以确保没有子行存在，为此他必须发出一个查询搜索列中的特定值，如果该列没有索引，那么服务器必须进行全表扫描。
- 索引哪些频繁被检索的列。

### 13.2 约束

约束是一种简单地强加于表中一列或多列的限制。

约束有以下几种不同的类型：

- 主键约束：标志一列或多列，并保证其值再表内的唯一性。
- 外键约束：限制一列或多列中的值必须来自于另一个表的主键或当前表的主键列。
- 唯一约束：限制一列或多列，保证其在表内的唯一性。
- 检查约束：限制一列的可用值范围。

#### 13.2.1 创建约束

约束一般于关联表通过CREATE TABLE语句同时创建。

示例：

    CREATE TABLE `product` (
      `product_cd` varchar(10) NOT NULL,
      `NAME` varchar(50) NOT NULL,
      `product_type_cd` varchar(10) NOT NULL,
      `date_offered` date DEFAULT NULL,
      `date_retired` date DEFAULT NULL,
      PRIMARY KEY (`product_cd`),
      KEY `fk_product_type_cd` (`product_type_cd`),
      CONSTRAINT `fk_product_type_cd` FOREIGN KEY (`product_type_cd`) REFERENCES `product_type` (`product_type_cd`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8

product表包含了两种约束：一个是指定了product_cd作为主键，另一个是指定了product_type_cd列作为引用product_type表的外键。

我们也可以先创建表，之后在添加约束：

    -- 添加主键约束
    ALTER TABLE product 
    ADD CONSTRAINT 
    pk_product PRIMARY KEY (product_cd);
    -- 添加外键约束
    ALTER TABLE product 
    ADD CONSTRAINT 
    fk_product_type_cd FOREIGN KEY (product_type_cd) 
    REFERENCES product_type (product_type_cd);

如果要删除约束，同样要使用ALTER TABLE语句：

    -- 删除主键（删除时要确认该主键没有被其他外键引用）
    ALTER TABLE product DROP PRIMARY KEY;
    -- 删除外键
    ALTER TABLE product DROP FOREIGN KEY fk_product_type_cd;

#### 13.2.2 约束与索引

不同的数据库对创建约束时是否创建索引这一问题有不同处理方式。

约束类型 | MySQL | SQL Server | Oracle
-----|-----|-----|-----
主键约束 | 生成唯一索引 | 生成唯一索引 | 使用已存在的索引或创建新索引
外键约束 | 生成索引 | 不生成索引 | 不生成索引
唯一性约束 | 生成唯一索引 | 生成唯一索引 | 使用已存在的索引或创建新索引

#### 13.2.3 级联约束

当使用外键约束时，如果我们试图插入新行或者修改行导致当前的外键列的值在所引用的父表的列中没有匹配的值，那么服务器就会抛出错误。

示例：

    SELECT * FROM product_type;

返回数据：父表，product_type，主键为product_type_cd

product_type_cd | NAME
-----|-----
ACCOUNT | Customer Accounts
INSURANCE | Insurance Offerings
LOAN | Individual and Business Loans

    SELECT * FROM product;

返回数据：子表，product，外键为product_type_cd，该外键引用`product_type.product_type_cd`

product_cd | NAME | product_type_cd | date_offered | date_retired
-----|-----|-----|-----|-----
AUT | auto loan | LOAN | 2000-01-01 | NULL
BUS | business line of credit | LOAN | 2000-01-01 | NULL
CD | certificate of deposit | ACCOUNT | 2000-01-01 | NULL
CHK | checking account | ACCOUNT | 2000-01-01 | NULL
MM | money market account | ACCOUNT | 2000-01-01 | NULL
MRT | home mortgage | LOAN | 2000-01-01 | NULL
SAV | savings account | ACCOUNT | 2000-01-01 | NULL
SBL | small business loan | LOAN | 2000-01-01 | NULL

下面试图将`product.product_type_cd`列的值更改为product_type表中根本不存在的值：

    UPDATE product 
    SET product_type_cd = 'XYZ' 
    WHERE product_type_cd = 'LOAN';

发生报错：

    Error Code: 1452
    Cannot add or update a child row: a foreign key constraint fails (`bank`.`product`, CONSTRAINT `fk_product_type_cd` FOREIGN KEY (`product_type_cd`) REFERENCES `product_type` (`product_type_cd`))

由于`product.product_type_cd`列存在外键约束，其约束引用的是`product_type.product_type_cd`列的值。而`product_type.product_type_cd`列并不存在值'XYZ'，所以发生报错。

下面试图`product_type.product_type_cd`列的值：

    UPDATE product_type 
    SET product_type_cd = 'XYZ' 
    WHERE product_type_cd = 'LOAN';

发生报错：

    Error Code: 1451
    Cannot delete or update a parent row: a foreign key constraint fails (`bank`.`product`, CONSTRAINT `fk_product_type_cd` FOREIGN KEY (`product_type_cd`) REFERENCES `product_type` (`product_type_cd`))

这是因为此时子表的外键中已经存在了值'LOAN'，这种情况下，在父表中再修改其引用的值就会导致子表中无法引用值'LOAN'，导致报错。

不过，我们可以设置外键约束的属性，使父表某行的变化传播到其对应的子表的所有子行，以保证数据完整性。

**设置级联更新：**

在添加外键约束时，使用`ON UPDATE CASCADE`语句可以设置级联更新。

示例：

    -- 删除原外键约束
    ALTER TABLE product 
    DROP FOREIGN KEY fk_product_type_cd;
    -- 重建外键约束，并允许级联更新
    ALTER TABLE product 
    ADD CONSTRAINT fk_product_type_cd FOREIGN KEY (product_type_cd) 
    REFERENCES product_type (product_type_cd) 
    ON UPDATE CASCADE;

修改了外键后，再次对`product_type.product_type_cd`列进行更新：

    UPDATE product_type 
    SET product_type_cd = 'XYZ' 
    WHERE product_type_cd = 'LOAN';

这时不再发生报错。

查询两个表：

    SELECT * FROM product_type;

返回数据：

product_type_cd | NAME
-----|-----
ACCOUNT | Customer Accounts
INSURANCE | Insurance Offerings
XYZ | Individual and Business Loans

    SELECT * FROM product;

返回数据：

product_cd | NAME | product_type_cd | date_offered | date_retired
-----|-----|-----|-----|-----
AUT | auto loan | XYZ | 2000-01-01 | NULL
BUS | business line of credit | XYZ | 2000-01-01 | NULL
CD | certificate of deposit | ACCOUNT | 2000-01-01 | NULL
CHK | checking account | ACCOUNT | 2000-01-01 | NULL
MM | money market account | ACCOUNT | 2000-01-01 | NULL
MRT | home mortgage | XYZ | 2000-01-01 | NULL
SAV | savings account | ACCOUNT | 2000-01-01 | NULL
SBL | small business loan | XYZ | 2000-01-01 | NULL

此时，对父表product_type的更新已经传播到了子表product。

除了级联更新，还可以指定级联删除。如果父表中的一行被删除，那么级联删除就会删除子表中对应的行。

**设置级联删除：**在添加外键约束时，使用`ON DELETE CASCADE`语句可以设置级联删除。

示例：

    -- 删除原外键约束
    ALTER TABLE product 
    DROP FOREIGN KEY fk_product_type_cd;
    -- 重建外键约束，并允许级联删除
    ALTER TABLE product 
    ADD CONSTRAINT fk_product_type_cd FOREIGN KEY (product_type_cd) 
    REFERENCES product_type (product_type_cd) 
    ON DELETE CASCADE;

此时，删除父表中的行，会导致子表中对应的行被删除。（但这个删除不一定会成功。例如，如果子表的主键又被第三个表的外键引用，此时会发生报错。不过报错原因不在于父表和子表，而在于这个第三个表）

## 第十四章 视图

>
本章致力于定义什么是视图，如何创建，何时以及如何使用它们。

### 14.1 什么是视图

视图是一种简单的数据查询机制。不同于表，**视图不涉及数据存储，即除了占用一点视图定义本身需要的空间外不占用其它磁盘空间。**

我们可以将CREATE语句和SELECT语句联用来创建视图，并供其他用户使用。而其他用户在使用这个视图时就像他们自己在查询数据表一样（事实上，他们可能不知道自己正在使用的是一个视图）。

示例：创建customer表的一个视图并掩盖部分fed_id

    CREATE VIEW customer_vw 
    (
        cust_id,
        fed_id,
        cust_type_cd,
        address,
        city,
        state,
        zipcode
    ) AS 
    SELECT cust_id,
           CONCAT('end in ', SUBSTR(fed_id, 8, 4)) fed_id,
           cust_type_cd,
           address,
           city,
           state,
           postal_code
    FROM customer;

上述语句第一部分列出了视图的列名（它们可能与基础表不同）；第二部分是一个SELECT语句，它的作用是为视图中的每列提供一个表达式。

或者也可以这样定义：

    CREATE VIEW customer_vw as select cust_id,
           concat('end in ', substr(fed_id, 8, 4)) fed_id,
           cust_type_cd,
           address,
           city,
           state,
           postal_code
    from customer;

一旦视图被创建，我们就可以将它作为一个表来查询。

示例：

    SELECT cust_id, fed_id, cust_type_cd FROM customer_vw;

返回数据：

cust_id | fed_id | cust_type_cd
-----|-----|-----
1 | end in 1111 | I
2 | end in 2222 | I
3 | end in 3333 | I
4 | end in 4444 | I
5 | end in 5555 | I
6 | end in 6666 | I
7 | end in 7777 | I
8 | end in 8888 | I
9 | end in 9999 | I
10 | end in 111 | B
11 | end in 222 | B
12 | end in 333 | B
13 | end in 444 | B

此时，服务器真正执行的查询不是我们上述提交的查询，也不是将提交的查询直接附加到视图定义而组成的查询，而是将两者合并创建的一个新查询。

上例中的新查询如下：

    SELECT cust_id, 
           CONCAT('end in ', SUBSTR(fed_id, 8, 4)) fed_id, 
           cust_type_cd 
    FROM customer;

如果想知道视图中有哪些可用的列，同样可以使用DESC命令：

    DESC customer_vw;

返回数据：

Field | Type | Null | Key | Default | Extra
-----|-----|-----|-----|-----|-----
cust_id | int(10) unsigned | NO |  | 0 | 
fed_id | varchar(11) | YES |  | NULL | 
cust_type_cd | enum('I','B') | NO |  | NULL | 
address | varchar(30) | YES |  | NULL | 
city | varchar(20) | YES |  | NULL | 
state | varchar(20) | YES |  | NULL | 
zipcode | varchar(10) | YES |  | NULL | 

对视图进行查询时，可以自由的使用SELECT语句的任何子句。

示例：

    SELECT cust_type_cd, COUNT(*) 
    FROM customer_vw 
    WHERE state = 'MA' 
    GROUP BY 1;

返回数据：

cust_type_cd | count(*)
-----|-----
I | 7
B | 2

同时也可以将视图与其他表/视图进行连接：

    SELECT cst.cust_id, cst.fed_id, bus.name
    FROM customer_vw cst 
    INNER JOIN business bus
    USING(cust_id);

返回数据：

cust_id | fed_id | name
-----|-----|-----
10 | end in 111 | Chilton Engineering
11 | end in 222 | Northeast Cooling Inc.
12 | end in 333 | Superior Auto Body
13 | end in 444 | AAA Insurance Inc.

### 14.2 为什么使用视图

#### 14.2.1 数据安全

如果我们创建一个表并允许用户查询，那么它们能够访问表中的每行每列。当表中包含敏感信息时，将数据对所有用户公开就不是一个好的做法了。

这种情况下最好的办法就是首先保持表的私有权限，例如不向用户授权SELECT许可。然后创建一个或多个视图，这些视图省略或掩盖了敏感内容。或者可以在视图定义中添加WHERE子句，以限制用户只能访问那些被允许的行。

#### 14.2.2 数据聚合

报表程序通常需要聚合数据，视图就是实现这一功能的好方法，使数据像已经被预聚合并存储在数据库一样。

示例，假设需要一个应用程序每月都生成报表展示账户数目和每个客户的储蓄总额，那么我们可以为程序的开发人员提供以下视图，而不是允许其直接查询基本表：

    CREATE VIEW customer_totals_vw
    (
        cust_id,
        cust_type_cd,
        cust_name,
        num_accounts,
        tot_deposits
    ) AS
    SELECT cst.cust_id,
           cst.cust_type_cd,
           CASE
                WHEN cst.cust_type_cd = 'B'
                    THEN
                    (
                        SELECT bus.name 
                        FROM business bus
                        WHERE bus.cust_id = cst.cust_id
                    )
                    ELSE
                    (
                        SELECT CONCAT(ind.fname, ' ', ind.lname)
                        FROM individual ind
                        WHERE ind.cust_id = cst.cust_id
                    )
                END cust_name,
           SUM(CASE WHEN act.status = 'ACTIVE' THEN 1 ELSE 0 END) tot_active_accounts,
           SUM(CASE WHEN act.status = 'ACTIVE' THEN avail_balance ELSE 0 END) tot_balance
    FROM customer cst 
    INNER JOIN account act
    USING(cust_id)
    GROUP BY 1, 2;

查询这个视图：

    SELECT * FROM customer_totals_vw;

返回数据：

cust_id | cust_type_cd | cust_name | num_accounts | tot_deposits
-----|-----|-----|-----|-----
1 | I | James Hadley | 3 | 4557.75
2 | I | Susan Tingley | 2 | 2458.02
3 | I | Frank Tucker | 2 | 3270.25
4 | I | John Hayward | 3 | 6788.98
5 | I | Charles Frasier | 1 | 2237.97
6 | I | John Spencer | 2 | 10122.37
7 | I | Margaret Young | 1 | 5000.00
8 | I | Louis Blake | 2 | 3875.18
9 | I | Richard Farley | 3 | 10971.22
10 | B | Chilton Engineering | 2 | 23575.12
11 | B | Northeast Cooling Inc. | 1 | 9345.55
12 | B | Superior Auto Body | 1 | 38552.05
13 | B | AAA Insurance Inc. | 1 | 50000.00

使用这种方法为数据库设计者提供了很大的灵活性。

#### 14.2.3 隐藏复杂性

部署视图最常见的理由之一是为终端用户屏蔽复杂性。

示例，假设需要每月创建一个报表展示雇员数目、活跃账户总数和每个分行的交易总数，那么我们应该提供如下视图，而不是让终端用户分别浏览4个表。

    CREATE VIEW branch_activity_vw
    (
        branch_name,
        city,
        state,
        num_employees,
        num_active_accounts,
        tot_transactions
    ) AS  
    SELECT br.name, 
           br.city, 
           br.state,
           (
                SELECT COUNT(*)
                FROM employee emp
                WHERE emp.assigned_branch_id = br.branch_id
           ) num_emps,
           (
                SELECT COUNT(*)
                FROM account acnt
                WHERE acnt.status = 'ACTIVE' AND acnt.open_branch_id = br.branch_id
           ) num_accounts,
           (
                SELECT COUNT(*)
                FROM TRANSACTION txn
                WHERE txn.execution_branch_id = br.branch_id
           ) num_txns
    FROM branch br;

查询这个视图：

    SELECT * FROM branch_activity_vw;

返回数据：

branch_name | city | state | num_employees | num_active_accounts | tot_transactions
-----|-----|-----|-----|-----|-----
Headquarters | Waltham | MA | 9 | 8 | 0
Woburn Branch | Woburn | MA | 3 | 7 | 0
Quincy Branch | Quincy | MA | 3 | 3 | 0
So. NH Branch | Salem | NH | 3 | 6 | 0

上述报表中6列中有3列的值是使用标量子查询生成的。如果某个用户使用了这个视图，但没有引用num_employees、num_active_accounts或tot_transactions列，那么一个子查询都不会执行。

#### 14.2.4 连接分区数据

为了提升性能，一些数据库在设计时会将大表分成多个小块。

例如，transaction表变得越来越大，我们就可以将其分为两个表：transaction_current，这个表保存最近6个月的数据；transaction_historic，这个表保存6个月前的所有数据。

此时，如果客户想查看特定账号的所有交易，就需要查询两个表。不过，通过查询两表的视图和联合两个查询结果，同样可以使苏哦有交易数据就像存储在一个单表中一样简单。

定义视图：

    CREATE VIEW transaction_vw
    (
        txn_date,
        account_id,
        txn_type_cd,
        amount,
        teller_emp_id,
        execution_branch_id,
        funds_avail_date
    ) AS
    SELECT txn_date,
           account_id,
           txn_type_cd,
           amount,
           teller_emp_id,
           execution_branch_id,
           funds_avail_date
    FROM transaction_historic
    UNION ALL
    SELECT txn_date,
           account_id,
           txn_type_cd,
           amount,
           teller_emp_id,
           execution_branch_id,
           funds_avail_date
    FROM transaction_current;

### 14.3 可更新的视图

对于MySQL来说，如果下面的条件都满足，那么视图就是可更新的：

- 没有使用聚合函数。
- 视图没有使用GROUP BY或HAVING子句。
- SELECT或FROM子句中不存在子查询，并且WHERE子句中的任何子查询都不引用FROM子句中的表。
- 视图没有使用UNION、UNION ALL和DISTINCT。
- FROM子句包括不止一个表或可更新视图。
- 如果有不止一个表或视图，那么FROM子句只使用内连接。

#### 14.3.1 更新简单视图

本章开头的视图定义：

    CREATE VIEW customer_vw 
    (
        cust_id,
        fed_id,
        cust_type_cd,
        address,
        city,
        state,
        zipcode
    ) AS 
    SELECT cust_id,
           CONCAT('end in ', SUBSTR(fed_id, 8, 4)) fed_id,
           cust_type_cd,
           address,
           city,
           state,
           postal_code
    FROM customer;

这个视图的定义没有违反前述的任何规则，所以可以使用这个视图来修改customer表中的数据。

示例：

    UPDATE customer_vw SET city = 'Woooburn' WHERE city = 'Woburn';

修改后对表customer进行查询：

    SELECT cust_id, city FROM customer;

返回数据：

cust_id | city
-----|-----
1 | Lynnfield
2 | Woooburn
3 | Quincy
4 | Waltham
5 | Salem
6 | Waltham
7 | Wilmington
8 | Salem
9 | Newton
10 | Salem
11 | Wilmington
12 | Salem
13 | Quincy

有查询结果可知视图修改成功。

按照这种方式我们可以修改该视图中的大多数列，但不能修改fed_id列，因为它是由一个表达式生成的导出列（导出列：不是直接对列进行引用，而是从表达式导出的）。

同时我们不能对customer_vw插入数据，因为包含导出列的视图不能用于插入数据。

#### 14.3.2 更新复杂视图

下面创建一个连接了business表和customer表的视图：

    CREATE VIEW business_customer_vw
    (
        cust_id,
        fed_id,
        address,
        city,
        state,
        postal_code,
        business_name,
        state_id,
        incorp_date
    ) AS
    SELECT cst.cust_id,
           cst.fed_id,
           cst.address,
           cst.city,
           cst.state,
           cst.postal_code,
           bsn.name,
           bsn.state_id,
           bsn.incorp_date
    FROM customer cst
    INNER JOIN business bsn
    USING(cust_id)
    WHERE cust_type_cd = 'B';

使用这个视图更新customer表或者business表：

    -- 该语句更新customer.postal_code列
    UPDATE business_customer_vw
    SET postal_code = '99999'
    WHERE cust_id = 10;
    -- 该语句更新business.incorp_date列
    UPDATE business_customer_vw
    SET incorp_date = '2008-11-7'
    WHERE cust_id = 10;

上述更新是被允许的，一次更新一个表。但如果想要在单个语句中更新两个表则会报错。

示例：

    UPDATE business_customer_vw
    SET postal_code = '88888', incorp_date = '2008-12-17'
    WHERE cust_id = 10;

发生报错：

    Error Code: 1393
    Can not modify more than one base table through a join view 'bank.business_customer_vw'

下面向两个表中插入一个新客户（cust_id = 99）:

    -- 向customer表插入数据
    INSERT INTO business_customer_vw 
    (cust_id, fed_id, address, city, state, postal_code)
    VALUES (99, '04-9999999', '99 Main St.', 'Peabody', 'MA', '01975');
    -- 向business表插入数据
    INSERT INTO business_customer_vw 
    (cust_id, business_name, state_id, incorp_date)
    VALUES (99, 'Ninety-Nine Restaurant', '99-999-999', '1999-01-01');

两个语句第一个成功，第二个报错：

    Error Code: 1393
    Can not modify more than one base table through a join view 'bank.business_customer_vw'

这是因为虽然两个表都包含cust_id列，但是视图定义中的cust_id列是映射到`customer.cust_id`列的，因此无法使用前面的视图定义向business表插入数据。

## 第十五章 元数据

>
本章讨论所谓的元数据是如何被存储的，存储在哪里，如何访问以及如何利用元数据构建灵活的系统。 

### 15.1 关于数据的数据

元数据本质上是关于数据的数据。每次创建数据库对象时，数据库服务器都要记录各种各样的信息。
例如，打算创建一个包含多列的表，它有主键约束、3个索引以及一个外键约束，那么服务器将需要保存以下信息：

- 表名
- 表存储信息（表空间、初始大小等）
- 存储引擎
- 列名
- 列数据类型
- 默认列值
- 非空列约束
- 主键列
- 主键名
- 主键索引名
- 索引名
- 索引类型
- 索引列
- 索引列排序顺序
- 索引存储信息
- 外键名
- 外键列
- 外键的关联表/列

上述数据统称为数据字典或者系统目录。数据库服务器需要不断地保存这个数据，同时，为了验证和执行SQL语句，它需要能快速地检索数据。此外，数据库服务器必须保护这个数据只能被通过恰当的机制修改，例如ALTER TABLE语句。

虽然存在用于服务器间进行元数据交换的标准，但是每个数据库服务器还是使用不同的机制提供元数据。例如：

- 一组视图，比如Oracle的usr_tables和all_constraints视图
- 一组系统存储过程，比如SQL Server的sp_tables过程或者Oracle的dbms_metadata
- 一组特殊数据库，比如MySQL的information_schema数据库

### 15.2 信息模式

Information_schema是MySQL自带的信息数据库。里面的“表”保存着服务器当前的实时信息。

实际上，整个information_schema及里面的所有表，都可以理解为视图。在需要访问到视图中数据时，通过创建临时表的方式，填充数据返回给客户端。information_schema数据库中的视图是可以被检索的，因此它可以被编程使用。

示例，检索bank数据库里所有表的名字：

    SELECT table_name, table_type 
    FROM information_schema.tables
    WHERE table_schema = 'bank'
    ORDER BY 1;

返回数据：

table_name | table_type
-----|-----
account | BASE TABLE
branch | BASE TABLE
branch_activity_vw | VIEW
business | BASE TABLE
business_customer_vw | VIEW
customer | BASE TABLE
customer_totals_vw | VIEW
customer_vw | VIEW
department | BASE TABLE
employee | BASE TABLE
employee_vw | VIEW
individual | BASE TABLE
officer | BASE TABLE
product | BASE TABLE
product_type | BASE TABLE
transaction | BASE TABLE

除了查询出基本表，同时也查询出了视图。

在结果中排除视图：

    SELECT table_name, table_type 
    FROM information_schema.tables
    WHERE table_schema = 'bank'
    AND table_type = 'BASE TABLE'
    ORDER BY 1;

返回数据：

table_name | table_type
-----|-----
account | BASE TABLE
branch | BASE TABLE
business | BASE TABLE
customer | BASE TABLE
department | BASE TABLE
employee | BASE TABLE
individual | BASE TABLE
officer | BASE TABLE
product | BASE TABLE
product_type | BASE TABLE
transaction | BASE TABLE

如果只对视图感兴趣，那么可以对`information_schema.views`进行查询。除了视图名称，还可以检索出其他信心，例如该视图是否可以更新：

    SELECT table_name, is_updatable
    FROM information_schema.views
    WHERE table_schema = 'bank'
    ORDER BY 1;

返回数据：

table_name | is_updatable
-----|-----
branch_activity_vw | NO
business_customer_vw | YES
customer_totals_vw | NO
customer_vw | YES
employee_vw | YES

也可以对一个表的列信息进行查询。

例如：

    SELECT column_name,
           data_type,
           character_maximum_length char_max_len,
           numeric_precision num_prcsn,
           numeric_scale num_scale
    FROM information_schema.columns
    WHERE table_schema = 'bank'
    AND table_name = 'account'
    ORDER BY ordinal_position;     

返回数据：

column_name | data_type | char_max_len | num_prcsn | num_scale
-----|-----|-----|-----|-----
account_id | int | NULL | 10 | 0
product_cd | varchar | 10 | NULL | NULL
cust_id | int | NULL | 10 | 0
open_date | date | NULL | NULL | NULL
close_date | date | NULL | NULL | NULL
last_activity_date | date | NULL | NULL | NULL
STATUS | enum | 6 | NULL | NULL
open_branch_id | smallint | NULL | 5 | 0
open_emp_id | smallint | NULL | 5 | 0
avail_balance | float | NULL | 10 | 2
pending_balance | float | NULL | 10 | 2

同样也可以通过检索`information_schema.statistics`视图获得某个表的索引信息。

示例：

    SELECT index_name, non_unique, seq_in_index, column_name
    FROM information_schema.statistics
    WHERE table_schema = 'bank' 
    AND table_name = 'account'
    ORDER BY 1, 3;

返回数据：

index_name | non_unique | seq_in_index | column_name
-----|-----|-----|-----
fk_a_branch_id | 1 | 1 | open_branch_id
fk_a_cust_id | 1 | 1 | cust_id
fk_a_emp_id | 1 | 1 | open_emp_id
fk_product_cd | 1 | 1 | product_cd
PRIMARY | 0 | 1 | account_id

可以通过检索`information_schema.constraints`视图获得已经创建的不同类型的约束信息。

示例：

    SELECT constraint_name, table_name, constraint_type 
    FROM information_schema.table_constraints
    WHERE table_schema = 'bank'
    ORDER BY 3, 1;

返回数据：

constraint_name | table_name | constraint_type
-----|-----|-----
fk_a_branch_id | account | FOREIGN KEY
fk_a_cust_id | account | FOREIGN KEY
fk_a_emp_id | account | FOREIGN KEY
fk_b_cust_id | business | FOREIGN KEY
fk_dept_id | employee | FOREIGN KEY
fk_exec_branch_id | transaction | FOREIGN KEY
fk_e_branch_id | employee | FOREIGN KEY
fk_e_emp_id | employee | FOREIGN KEY
fk_i_cust_id | individual | FOREIGN KEY
fk_o_cust_id | officer | FOREIGN KEY
fk_product_cd | account | FOREIGN KEY
fk_product_type_cd | product | FOREIGN KEY
fk_teller_emp_id | transaction | FOREIGN KEY
fk_t_account_id | transaction | FOREIGN KEY
PRIMARY | individual | PRIMARY KEY
PRIMARY | account | PRIMARY KEY
PRIMARY | branch | PRIMARY KEY
PRIMARY | department | PRIMARY KEY
PRIMARY | transaction | PRIMARY KEY
PRIMARY | customer | PRIMARY KEY
PRIMARY | officer | PRIMARY KEY
PRIMARY | product_type | PRIMARY KEY
PRIMARY | business | PRIMARY KEY
PRIMARY | product | PRIMARY KEY
PRIMARY | employee | PRIMARY KEY

**常用的information_schema视图：**

视图名称 | 提供的相关信息
-----|-----
Schemata | 数据库
Tables | 表和视图
Columns | 表和视图的列
statistics | 索引
User_Privileges | 模式权限分配 
Schema_Privileges | 数据库权限分配
Table_Privileges | 表权限分配
Column_Privileges | 列权限分配
Character_Sets | 可用字符集
Collations | 各字符集对照信息
Collations_Character_Set_Applicability | 可用于校对的字符集
Table_Constraints | 唯一、主键和外键约束
Key_Column_Usage | 于每个键列相关的约束
Routines | 存储例程（过程和函数）
Views | 视图
Triggers | 表触发器
Plugins | 服务器插件程序
Engines | 可用存储引擎
Partitions | 表分区 
Events | 预定事件
Process_List | 正在运行的程序
Referential_Constraints | 外键
Global_Status | 服务器状态信息
Session_Status | 会话状态信息
Global_Variables | 服务器状态变量
Session_Variables | 会话状态变量
Parameters | 存储过程和函数参数
Profiling | 用户配置信息

### 15.3 使用元数据

本节介绍几种可以在程序中使用元数据的方法。

#### 15.3.1 模式生成脚本

我们可能需要生成一个脚本，他能够创建小组已经部署的各种表、索引、视图等。虽然各种工具和程序可以生成这种类型的脚本，但我们也可以通过查询information_shcema视图，然后生成脚本

示例，生成创建customer表的查询：

    SELECT 'CREATE TABLE customer (' create_table_statement
    UNION ALL
    SELECT cols.txt
    FROM
    (
        SELECT CONCAT('   ', column_name, '  ', column_type,
            CASE 
                WHEN is_nullable = 'NO' THEN ' not null'
                ELSE ''
            END,
            CASE
                WHEN extra IS NOT NULL THEN CONCAT('  ', extra)
                ELSE ''
            END,
            ',' 
        ) txt
        FROM information_schema.columns
        WHERE table_schema = 'bank' AND table_name = 'customer'
        ORDER BY ordinal_position
    ) cols
    UNION ALL
    SELECT ')';

返回数据：

    create_table_statement
    CREATE TABLE customer (
       cust_id  int(10) unsigned not null  auto_increment,
       fed_id  varchar(12) not null  ,
       cust_type_cd  enum('I','B') not null  ,
       address  varchar(30)  ,
       city  varchar(20)  ,
       state  varchar(20)  ,
       postal_code  varchar(10)  ,
    )

添加主键约束信息：

    SELECT 'CREATE TABLE customer (' create_table_statement
    UNION ALL
    SELECT cols.txt
    FROM
    (
        SELECT CONCAT('   ', column_name, '  ', column_type,
            CASE 
                WHEN is_nullable = 'NO' THEN ' not null'
                ELSE ''
            END,
            CASE
                WHEN extra IS NOT NULL THEN CONCAT('  ', extra)
                ELSE ''
            END,
            ',' 
        ) txt
        FROM information_schema.columns
        WHERE table_schema = 'bank' AND table_name = 'customer'
        ORDER BY ordinal_position
    ) cols
    UNION ALL
    SELECT CONCAT('  constraint primary key (') 
    FROM information_schema.table_constraints
    WHERE table_schema = 'bank' AND table_name = 'customer'
    AND constraint_type = 'PRIMARY KEY'
    UNION ALL
    SELECT cols.txt
    FROM 
    (
        SELECT CONCAT(CASE WHEN ordinal_position > 1 THEN '   ,' ELSE '   ' END, column_name) txt
        FROM information_schema.key_column_usage
        WHERE table_schema = 'bank' AND table_name = 'customer'
        AND constraint_name = 'PRIMARY'
        ORDER BY ordinal_position
    ) cols
    UNION ALL 
    SELECT '  )'
    UNION ALL
    SELECT ')';

返回数据：

    create_table_statement
    CREATE TABLE customer (
       cust_id  int(10) unsigned not null  auto_increment,
       fed_id  varchar(12) not null  ,
       cust_type_cd  enum('I','B') not null  ,
       address  varchar(30)  ,
       city  varchar(20)  ,
       state  varchar(20)  ,
       postal_code  varchar(10)  ,
      constraint primary key (
       cust_id
      )
    )

#### 15.3.2 部署验证

许多组织允许有数据库维护窗口，在那里可以管理现有的数据库对象（例如添加、删除分区），以及部署新模式和新对象。在部署脚本运行后，我们可以运行验证脚本以确保新的模式对象具有合适的列、索引、主键等。

示例，以下查询返回bank模式中每个表的列数、索引数以及主键约束数（0或1）：

    SELECT tbl.table_name,
    (
        SELECT COUNT(*) FROM information_schema.columns clm
        WHERE clm.table_schema = tbl.table_schema
        AND clm.table_name = tbl.table_name
    ) num_columns,
    (
        SELECT COUNT(*) FROM information_schema.statistics sta
        WHERE sta.table_schema = tbl.table_schema
        AND sta.table_name = tbl.table_name
    ) num_indexes,
    (
        SELECT COUNT(*) FROM information_schema.table_constraints tc
        WHERE tc.table_schema = tbl.table_schema
        AND tc.table_name = tbl.table_name
        AND tc.constraint_type = 'PRIMARY KEY'
    ) num_primary_keys
    FROM information_schema.tables tbl
    WHERE tbl.table_schema = 'bank' 
    AND table_type = 'BASE TABLE'
    ORDER BY 1;

返回数据：

table_name | num_columns | num_indexes | num_primary_keys
-----|-----|-----|-----
account | 11 | 5 | 1
branch | 6 | 1 | 1
business | 4 | 1 | 1
customer | 7 | 1 | 1
department | 2 | 1 | 1
employee | 9 | 4 | 1
individual | 4 | 1 | 1
officer | 7 | 2 | 1
product | 5 | 2 | 1
product_type | 2 | 1 | 1
transaction | 8 | 4 | 1

#### 15.3.3 生成动态SQL

大多数数据库允许SQL语句以字符串形式提交。提交字符串给数据库引擎而不是使用它的SQL接口通常被称为动态SQL执行。

MySQL为动态SQL执行提供了prepare、exeucte和deallocate语句。

示例：

    -- 将字符串赋予变量qry
    SET @qry = 'select cust_id, cust_type_cd, fed_id from customer';
    -- qry被prepare语句提交给数据库引擎（解析、安全减产和优化）
    PREPARE dynsql1 FROM @qry;
    -- 执行语句
    EXECUTE dynsql1;
    -- 关闭语句，释放执行中使用的数据库资源
    DEALLOCATE PREPARE dynsql1;

上述语句返回数据：

cust_id | cust_type_cd | fed_id
-----|-----|-----
1 | I | 111-11-1111
2 | I | 222-22-2222
3 | I | 333-33-3333
4 | I | 444-44-4444
5 | I | 555-55-5555
6 | I | 666-66-6666
7 | I | 777-77-7777
8 | I | 888-88-8888
9 | I | 999-99-9999
10 | B | 04-1111111
11 | B | 04-2222222
12 | B | 04-3333333
13 | B | 04-4444444

通过在查询中包含占位符，也可以在运行时动态指定条件。

示例：

    SET @qry = 'select product_cd, name, product_type_cd, date_offered, date_retired from product where product_cd = ?';
    PREPARE dynsql2 FROM @qry;
    SET @prodcd = 'CHK';
    EXECUTE dynsql2 USING @prodcd;
    SET @prodcd = 'SAV';
    EXECUTE dynsql2 USING @prodcd;

返回数据：

product_cd | name | product_type_cd | date_offered | date_retired
-----|-----|-----|-----|-----
CHK | checking account | ACCOUNT | 2000-01-01 | NULL

product_cd | name | product_type_cd | date_offered | date_retired
-----|-----|-----|-----|-----
SAV | savings account | ACCOUNT | 2000-01-01 | NULL

上述查询包含一个占位符，所欲i产品代码可以在运行时被提交。

使用动态SQL查询表，从视图`information.colums`中检索列名：

    SELECT CONCAT('SELECT ',
                    CONCAT_WS(',', cols.col1, cols.col2, cols.col3, cols.col4, cols.col5, cols.col6, cols.col7, cols.col8, cols.col9),
                    ' FROM product WHERE product_cd = ?')
    INTO @qry
    FROM
    (
        SELECT MAX(CASE WHEN ordinal_position = 1 THEN column_name ELSE NULL END) col1, 
               MAX(CASE WHEN ordinal_position = 2 THEN column_name ELSE NULL END) col2,
               MAX(CASE WHEN ordinal_position = 3 THEN column_name ELSE NULL END) col3, 
               MAX(CASE WHEN ordinal_position = 4 THEN column_name ELSE NULL END) col4, 
               MAX(CASE WHEN ordinal_position = 5 THEN column_name ELSE NULL END) col5, 
               MAX(CASE WHEN ordinal_position = 6 THEN column_name ELSE NULL END) col6, 
               MAX(CASE WHEN ordinal_position = 7 THEN column_name ELSE NULL END) col7, 
               MAX(CASE WHEN ordinal_position = 8 THEN column_name ELSE NULL END) col8, 
               MAX(CASE WHEN ordinal_position = 9 THEN column_name ELSE NULL END) col9
        FROM information_schema.columns
        WHERE table_schema = 'bank' AND table_name = 'product'
        GROUP BY table_name
    ) cols;

    PREPARE dynsql3 FROM @qry;

    SET @prodcd = 'MM';

    EXECUTE dynsql3 USING @prodcd;

    DEALLOCATE PREPARE dynsql3;

返回数据：

product_cd | NAME | product_type_cd | date_offered | date_retired
-----|-----|-----|-----|-----
MM | money market account | ACCOUNT | 2000-01-01 | NULL

这个查询以product表的前9列为核心，先使用CONCAT()和CONCAT_WS函数构建查询字符串，然后将字符串赋予变量qry，最后查询字符串像前面的查询一样被执行。

## 附录 B MySQL对SQL语言的扩展

### B.1 扩展SELECT语句

#### B.1.1 LIMIT子句

MySQL允许在SELECT语句中使用LIMIT子句，它限制查询返回的行数。

示例，首先查询银行中每个柜员开立的账户数

    SELECT open_emp_id, COUNT(*) how_many
    FROM account
    GROUP BY 1;

返回数据：

open_emp_id | how_many
-----|-----
1 | 8
10 | 7
13 | 3
16 | 6

如果想限制结果集到3条记录，那么可以添加一个LIMIT子句指定只有3条记录被返回：

    SELECT open_emp_id, COUNT(*) how_many
    FROM account
    GROUP BY 1
    LIMIT 3;

返回数据：

open_emp_id | how_many
-----|-----
1 | 8
10 | 7
13 | 3

**组合LIMIT子句和ORDER BY子句：**

上述返回3条记录的查询有一个小问题，即我们不能描述究竟要返回4条中的哪3条。如果要查找特定得3条记录，可以结合ORDER BY子句：

    SELECT open_emp_id, COUNT(*) how_many
    FROM account
    GROUP BY 1
    ORDER BY 2 DESC
    LIMIT 3;

返回数据：

open_emp_id | how_many
-----|-----
1 | 8
10 | 7
16 | 6

*LIMIT子句应用在所有过滤、分组和排序动作完成之后，因此它永远不会改变SELECT语句的结果，而只是限制查询返回记录的数目。*

组合了ORDER BY和LIMIT子句的查询允许将数据进行排名，故这种查询可以称为排名查询。

**LIMIT子句的第二个可选参数：**

LIMIT子句允许可选的第二个参数，此时第一个指定结果集的起始记录，第二个参数指定结果集中包含的记录数。**MySQL指定第一个记录序号为0。**

示例，查找开户数量第三多的人：

    SELECT open_emp_id, COUNT(*) how_many
    FROM account
    GROUP BY 1
    ORDER BY 2 DESC
    LIMIT 2, 1;

返回结果：

open_emp_id | how_many
-----|-----
16 | 6

在上述例子中，第0和第1条记录被丢弃了，因为结果集是从第2个记录开始的。由于LIMIT子句的第二个参数是1，所以结果集只包含1条记录。

如果希望从第2个记录开始，并且包含所有剩下的记录，那么可以这样做：

    SELECT open_emp_id, COUNT(*) how_many
    FROM account
    GROUP BY 1
    ORDER BY 2 DESC
    LIMIT 2, 100000000;

返回数据：

open_emp_id | how_many
-----|-----
16 | 6
13 | 3

使第二个参数足够大到超过剩下的记录数即可。

#### B.1.2 INTO OUTFILE子句

如果希望将查询的结果写到一个文件中，我们可以直接复制查询结果然后粘贴到文件中。但如果查血结果集非常大或者查询从一个脚本执行，那么就需要一个方法自动将结果写入文件。

INTO OUTFILE子句，允许提供一个文件名，并把查询结果写入这个文件中。

示例：

    SELECT emp_id, fname, lname, start_date
    INTO OUTFILE 'C:\\Users\\Tavish\\Desktop\\tmp.txt'
    FROM employee;

默认格式在列间使用（\t）隔开，记录间使用（\n）隔开。

当然也可以调整格式：

    SELECT emp_id, fname, lname, start_date
    INTO OUTFILE 'C:\\Users\\Tavish\\Desktop\\tmp.txt'
    FIELD TERMINATED BY '|'
    LINES TERMINATED BY '@'
    FROM employee;

此时列间使用（|）隔开，记录间使用（@）隔开。

如果想要生成数据，用于电子表格程序，或者在自己的组织内外传送，那么INTO OUTFILE子句可以提供足够的弹性，以实现需要的文件格式。

*使用INTO OUTFILE子句时，MySQL不允许覆盖现有文件，因此如果要多次运行同一个查询，就需要先移除已存在的文件。*

### B.2 组合INSERT/UPDATE语句

假设现在要创建一个表以记录哪些客户访问了哪些分行这个信息。这个表要包括客户ID列，分行ID列和日期列（描述客户访问该支行的最后事件）。无论客户何时访问分行，表中都会增加一列，但是如果客户已经存在，那么只需简单地对存在的行的DATETIME列进行修改。

表的定义：

    CREATE TABLE branch_usage
    (
        branch_id SMALLINT UNSIGNED NOT NULL,
        cust_id INTEGER UNSIGNED NOT NULL,
        last_visited_on DATETIME,
        CONSTRAINT pk_branch_usage PRIMARY KEY (branch_id, cust_id)
    );

除了三个列的定义，branch_usage表还设置了联合主键，避免任何重复的客户/分行对。

假设ID为5的客户在第一周访问ID为1的分行3次。

当第一次访问时，我们可以向branch_usage表中插入记录：

    INSERT INTO branch_usage VALUES(1, 5, CURRENT_TIMESTAMP());

当第二次访问时，由于主键约束我们需要更新该记录而不是插入新记录。否则会发生报错。为了避免这种错误，我们可以先查询branch_usage表以判断一个指定的客户/支行对是否已经存在，然后插入一个记录（如果不存在该记录）或者更新一个存在的记录（如果已存在该记录）。

上述做法很麻烦，为了解决这个问题MySQL扩展了INSERT语句，从而允许在INSERT语句由于重复键而失败时修改一列或多列。

示例，当给定的客户/分行对已经存在于表中时修改last_visited_on列：

    INSERT INTO branch_usage 
    VALUES(1, 5, CURRENT_TIMESTAMP()) 
    ON DUPLICATE KEY UPDATE last_visited_on = CURRENT_TIMESTAMP();

这种操作通常被称为更新插入。

### B.3 按排序更新和删除

MySQL允许在UPDATE和DELETE中使用LIMIT和ORDER BY子句，因此可以实现在表中根据次序修改或删除特定的行。

假设现在需要在包含顾客在网上银行的登录信息的表中删除一些记录，该表记录顾客ID和登录日期/时间。

表定义：

    CREATE TABLE login_history
    (
        cust_id INTEGER UNSIGNED NOT NULL,
        login_Date DATETIME,
        CONSTRAINT pk_login_history PRIMARY KEY (cust_id, login_date)
    );

添加一些数据：

    INSERT INTO login_history 
    SELECT c.cust_id, 
           ADDDATE(a.open_date, INTERVAL a.account_id * c.cust_id HOUR)
    FROM customer c CROSS JOIN account a;

现在该表共有312行数据。现在我们要保留该表的最近50条记录并删除其他所有记录。

方法一：使用ORDER BY和LIMIT子句，找出前50个最近登录的记录

    SELECT cust_id, login_date
    FROM login_history
    ORDER BY 2 DESC
    LIMIT 49, 1;

返回数据：

cust_id | login_date
-----|-----
3 | 2004-07-02 21:00:00

借助该信息，可以构建DELETE语句，以删除所有login_date小于上述查询所返回日期的所有记录：

    DELETE FROM login_history WHERE login_date < '2004-07-02 21:00:00';

方法2：组合使用DELETE、ORDER BY、LIMIT

    DELETE FROM login_history
    ORDER BY login_date ASC
    LIMIT 262;

使用该语句，表中的行将按照login_date升序排列，然后删除前262行，保留最近的50行。

如果实现不知道原有表的行数，下面的做法更好一些：

    DELETE FROM login_history
    ORDER BY login_date DESC
    LIMIT 49, 9999999;

即以降序排列并告知服务器跳过前50行后再删除其余行。

除了DELETE语句，UPDATE语句也可以使用LIMIT子句和ORDER BY子句。

示例，银行决定为10个开户最久的账户增加$100作为奖励：

    UPDATE account SET avail_balance = avail_balance + 100
    WHERE product_cd IN ('CHK', 'SAV', 'MM')
    ORDER BY open_date ASC
    LIMIT 10;

*MySQL不允许在DELETE或UPDATE语句中使用LIMIT子句时提供第二个参数。*

### B.4 多表更新与删除

在某些条件下，我们需要从几个表中修改或删除数据以完成给定的任务。

例如，当我们发现银行的数据库中包含了在系统测试时添加的虚假客户时，就需要从account、customer和individual表中删除数据。

*以下示例使用克隆表，克隆自individual2、customer2和account2。*

    CREATE TABLE individual2 AS SELECT * FROM individual;
    CREATE TABLE customer2 AS SELECT * FROM customer;
    CREATE TABLE account2 AS SELECT * FROM account;

如果虚假的客户ID为1，那么可以为3个表分别创建3条独立的DELETE语句：

    DELETE FROM account2 WHERE cust_id = 1;
    DELETE FROM customer2 WHERE cust_id = 1;
    DELETE FROM individual2 WHERE cust_id = 1;

除了上述方法外，MySQL允许编写单条多重删除语句完成同样的工作：

    DELETE account2, customer2, individual2 
    FROM account2 
    INNER JOIN customer2 USING(cust_id)
    INNER JOIN individual2 USING(cust_id)
    WHERE individual2.cust_id = 1;

除了可以从多个表中删除行，MySQL还允许使用多表UPDATE语句同时修改多个表中的行。

示例，假设当前银行要与另一家银行合并，而这两家银行的数据库中含有重叠的cust_id。现在决定要将数据库中的每个cust_id增加10000，以便第二家银行的数据能够安全地导入：

    -- 以cust_id = 3为例
    UPDATE individual2 
    INNER JOIN customer2 USING(cust_id)
    INNER JOIN account2 USING(cust_id)
    SET individual2.cust_id = individual2.cust_id + 10000,
        customer2.cust_id = customer2.cust_id + 10000,
        account2.cust_id = account2.cust_id + 10000
    WHERE individual2.cust_id = 3;

**如果使用的InnoDB存储引擎，并且表之间存在外键约束，那么不能使用多表DELETE或UPDATE语句。**