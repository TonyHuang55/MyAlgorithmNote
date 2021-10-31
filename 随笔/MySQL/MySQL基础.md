# MySQL

## 数据库的基本操作

### 创建数据库
```
CREATE DATABASE [IF NOT EXISTS] db_name
        [create_specification[,create_specification]...]
```
create_specification:
* [DEFAULT] CHARACTER SET charset_name    字符集
* [DEFAULT] COLLATE       collation_name  字符集校对规则，

|字符集|字符集校对规则|
|:---:|:---:|
|默认 utf-8|常用 utf8_general_ci(不区分大小写)</br>utf8_bin(区分大小写)|

### 查看/删除数据库
显示所有数据库：
```
SHOW DATABASES
```
显示数据库创建语句：
```
SHOW CREATE DATABASE db_name
```
删除数据库：
```
DROP DATABASE [IF EXISTS] db_name
```

### 备份恢复数据库
备份数据库(注意：在 DOS 执行)
```
mysqldump -u 用户名 -p -B 数据库1 数据库2 数据库n > 文件名.sql
```
备份数据库的表
```
mysqldump -u 用户名 -p 数据库 表1 表2 表n > 文件名.sql
```
恢复数据库(注意：进入 MySQL 命令行再执行)
```
Source 文件名.sql
```

## 表的基本操作
### 创建
```
CREATE TABLE table_name
(
    field1 datatype,
    field2 datatype,
    field3 datatype
)character set 字符集 collate 校对规则 engine 存储引擎
```
* field：指定列名
* datatype：指定列类型
* character set：如不指定则为所在数据库的字符集
* collate：如不指定则为所在数据库校对规则
* engine：存储引擎

### 删除
```
DROP TABLE table_name;
```
### 修改
查看表结构
```
DESC table_name;
```
增加列
```
ALTER TABLE table_name
    ADD (column datatype[,column datatype]...);
```
修改列
```
ALTER TABLE table_name
    MODIFY (column datatype[,column datatype]...);
```
修改列名
```
ALTER TABLE table_name
    CHANGE old_column_name new_column_name datatype;
```
删除列
```
ALTER TABLE table_name
    DROP (column[,column]...);
```
修改表名
```
RENAME TABLE old_name TO new_name;
```
修改表字符集
```
ALTER TABLE table_name CHARACTER SET charset_name
```
### 复制
从其他表复制数据
```
INSERT INTO target_table
    (tar_col1,tar_col2[,...])
    SELECT org_col1,... 
        FROM origin_table;
```

自我复制
```
INSERT INTO table_name
    SELECT * FROM table_name;
```

补充：
    
    有重复数据的表 table 如何去除重复记录？
    1. 创建一张结构和原表完全一致的临时表 tmp。
    2. 通过 DISTINCT 关键字将 table 的数据复制到 tmp。
    3. 清除 table 所有记录。
    4. 将 tmp 所有记录复制到 table。
    5. 删除 tmp 表。

## MySQL 数据类型
数值类型

|数据类型|说明|
|---|---|
|BIT(M)|位类型。M 指定位数，默认值 1，范围 1 ～ 64|
|TINYINT [UNSIGNED] 1 个字节|带符号范围 -128 ～ 127。</br>无符号 0 ～ 255。</br>默认是有符号|
|SMALLINT [UNSIGNED] 2 个字节|带符号范围 - 2 ^ 15 ~ 2 ^ 15 - 1。</br>无符号 0 ~ 2 ^ 16 - 1|
|MEDIUMINT [UNSIGNED 3 个字节|带符号范围 - 2 ^ 23 ~ 2 ^ 23 - 1。</br>无符号 0 ~ 2 ^ 24 - 1||
|INT [UNSIGNED] 4 个字节|带符号范围 - 2 ^ 31 ~ 2 ^ 31 - 1。</br>无符号 0 ~ 2 ^ 32 - 1|
|BIGINT [UNSIGNED] 8 个字节|带符号范围 - 2 ^ 63 ~ 2 ^ 63 - 1。</br>无符号 0 ~ 2 ^ 64 - 1|
|FLOAT [UNSIGNED]|单精度，占用空间 4 个字节|
|DOUBLE [UNSIGNED|双精度，表示比 float 精度更大的小数，占用 8 个字节|
|DECIMAL(M,D) [UNSIGNED]|定点数，M 指定长度(最大 65)，D 表示小数点的位数(最大 30)|

整数使用细节：
1. 如果没有额外指定，默认有符号
2. 如果需要使用无符号，则要在数据类型后加上 ```UNSIGNED```

位类型使用细节：
1. bit 字段显示时，按照**位**的方式显示
2. 查询的时候仍然可以用使用添加的数值
3. 如果一个字段取值只有 0、1，可以考虑用 bit(1) ，节约空间

***

文本、二进制类型

|数据类型|说明|
|---|---|
|CHAR(size)|固定长度字符串，最大 255 字符|
|VARCHAR(size)|可变长度字符串，最大 65535(2 ^ 16 - 1) 字节|
|BLOB LONGBLOB|二进制数据</br>BLOB 0 ~ 2 ^ 16 - 1</br>LONGBLOB 0 ~ 2 ^ 32 - 1|
|TEXT LONGTEXT|文本</br>TEXT 0 ~ 2 ^ 16 - 1</br>LONGTEXT 0 ~ 2 ^ 32 - 1|

字符串使用细节：
1. VARCHAR 最大存储 65532 字节，1 ~ 3 个字节用于记录字段的大小。假如使用 utf-8 编码则最多存放 21844 个字符；假如使用 GBK 编码则最多存放 32766 个字符。
2. CHAR(size) 和 VARCHAR(size) 里的 size 指的是字符，而非字节。占用的空间取决于使用的编码。
3. CHAR 是定长，如果加入的数据长度不足，会引发空间的浪费。
4. VARCHAR 是变长的，实际占用的空间 = 数据内容 + 记录内容长度的 1 ~ 3 个字节
5. **查询速度：CHAR > VARCHAR**
6. 存放文本可以使用 TEXT 代替 VARCHAR。

***

时间日期

|数据类型|说明|
|---|---|
|DATE|日期类型(YYYY-MM-DD)|
|YEAR|年(YYYY)|
|TIME|时间类型(HH:mm:ss)|
|DATATIME|(YYYY-MM-DD HH:mm:ss)|
|TIMESTAMP|时间戳，可用于自动记录 insert、update 操作的时间|

时间戳自动更新配置方法：
```
CREATE TABLE table_name
(
    field1 TIMESTAMP 
        NOT NULL DEFAULT CURRENT_TIMESTAMP  -- 不允许为空，默认当前时间戳
        ON UPDATE CURRENT_TIMESTAMP         -- 自动以更新时间更新
);
```

## CRUD (Create + Read + Update + Delete)
### Insert
```
INSERT INTO table_name [(column[,column...])]
    VALUES (value[,value...])
         [,(value[,value...])...];
```
插入细节：
1. 插入的数据和字段类型要一致、长度要合法、位置要对应匹配。
2. 字符串和日期类型需要用单引号 '' 包裹。
3. 字段允许 null 时，允许插入空值。
4. 当不给某个字段插入值时，如果有默认值会默认添加，否则会报错；如果某个列没有指定 not null，那么当插入数据时不给该字段添加值，默认添加 NULL。

### Update
```
UPDATE table_name
    SET column_name1 = expr1[,column_name2 = expr2...]
    [WHERE where_definition]
```
更新细节：
1. UPDATE 不带 WHERE 子句会更新所有的记录。

### Delete
```
DELETE FROM table_name
    [WHERE where_definition]
```
删除细节：
1. DELETE 不带 WHERE 子句会删除所有的记录。
2. DELETE 并不难删除某一列的值(可以用 UPDATE 更新为 NULL 或 '')。
3. DELETE 仅删除记录，不删除表本身。

### Select
#### 单表查询
##### 常用的 Select 语句书写 
普通查询(DISTINCT 去重)
```
SELECT [DISTINCT] *|{column1,column2...}
    FROM table_name;
```
对查询的列进行计算
```
SELECT expression1,expression2...
    FROM table_name;
```
AS 语句起别名
```
SELECT column_name AS 别名
    FROM table_name;
```
ORDER BY 排序(位于 SELECT 语句结尾)
```
SELECT column1,column2...
    FROM table_name
    ORDER BY column asc|desc    -- asc升序(默认)，desc降序
```
GROUP BY (HAVING) 分组过滤
```
SELECT column1,column2...
    FROM table_name
    GROUP BY column HAVING ...;
```
LIMIT 分页查询
```
SELECT * 
    FROM table_name
    LIMIT start,rows;   -- 表示从 start + 1 行开始取出 rows 行。start 从 0 开始计算。
```

**字句顺序：GROUP BY、HAVING、ORDER BY、LIMIT**

##### WHERE 字句中常用的运算符
比较运算符

|运算符|含义|
|---|---|
|>、<、<=、>=、=、<>、!=|大于、小于、大于(小于)等于、不等于|
|BETWEEN ... AND ...|显示再某一区间的值(左闭右闭区间)|
|IN(set)|显示在 IN 列表中的值|
|(NOT) LIKE \` \`|模糊查询</br>'%' 代表 0 ～ n 个占位字符</br>'_' 代表单个占位字符|
|IS NULL|判断是否为空|

逻辑运算符

|运算符|含义|
|---|---|
|AND|多个条件同时成立|
|OR|多个条件任一成立|
|NOT|不成立|

##### 合并查询

|关键字|含义及用法|
|---|---|
|UNION ALL|将两个查询结果简单合并(不去重)|
|UNION|将两个查询结果去重合并|

#### 多表查询
笛卡尔集：

* 默认情况下查询两个表，会从第一张表中依次取出一行与第二张表的每一行组合，返回的结果是笛卡尔集，条目数量是 rows1 *  rows2 。
* 如果多表查询的条件少于 (表的个数 - 1) ，则会出现笛卡尔集。

自连接
* 将一张表当作两张表使用进行多表查询时，必须为表起别名。

#### 子查询
* 单行子查询的结果可以直接判断。
* 多行子查询的结果需要使用关键字 IN (集合)。
* 子查询可以当作临时表。
* 子查询中使用 ALL / ANY 有时可以用 MAX / MIN 替换。
* 多列子查询需要用多列的语法进行匹配：```SELECT * FROM table_name WHERE (col1,col2) = (子查询)```

## 函数
### 统计函数
#### COUNT
```
SELECT COUNT(*)|COUNT(column_name)
    FROM table_name
    WHERE where_definition;
```

</br>

COUNT(*) 和 COUNT(column) 的区别：
* COUNT(*) ：统计满足条件的记录的行数。
* COUNT(column) ：统计满足条件的**非空记录**的行数。

### 合计函数
#### SUM
```
SELECT SUM(column_name){,SUM(column_name)...}
    FROM table_name
    WHERE where_definition;
```

#### AVG
```
SELECT AVG(column_name){,AVG(column_name)...}
    FROM table_name
    WHERE where_definition;
```

#### MAX / MIN
```
SELECT MAX|MIN(column_name)
    FROM table_name
    WHERE where_definition;
```

### 时间日期

|函数|用途|
|---|---|
|CURRENT_DATE()|当前日期|
|CURRENT_TIME()|当前时间|
|CURRENT_TIMESTAMP()|当前时间戳|
|DATE(datetime)|返回 datetime 的日期部分|
|DATE_ADD(date,INTERVAL d_value d_type)|在 date 基础上加上日期或时间|
|DATE_SUB(date,INTERVAL d_value d_type)|在 date 基础上减去日期或时间|
|DATEDIFF(date1,date2)|返回两个日期的日期差(天)|
|TIMEDIFF(time1,time2)|返回两个时间的时间差(HH:mm:ss)|
|NOW()|返回当前时间|
|YEAR \| MONTH \| DATE(datetime) FROM_UNIXTIME|返回年/月/日|
|UNIX_TIMESTAMP()|返回 1970-01-01 到现在的秒数|
|FROM_UNIXTIME</br>(timestamp,%Y-%m-%d %H:%i:%s)|将 UNIX_TIMESTAMP 的时间戳结果转换为制定格式的日期|

注意事项：
* DATE_ADD 和 DATE_SUB 中的 INTERVAL 后面可以是 year、month、day、hour、minute、second 等。
* DATEDIFF(date1，date2) 得到的是 date1 - date2 的天数，可以是负数。
* 日期类型可以直接比较，但是要注意格式的匹配。

### 字符串函数

|函数|用途|
|---|---|
|CHARSET(str)|返回字符串的字符集|
|CONCAT(str[,...])|连接字符串|
|INSTR(str,substring)|返回 substring 在 str 中出现的位置，若未出现返回 0|
|UCASE \| LCASE(str)|转换大/小写|
|LEFT \| RIGHT(str,length)|从 str 左起/右起取 length 个字符|
|LENGTH(str)|返回字符串的长度(按照字节)|
|REPLACE(str,search_str,replace_str)|在 str 中用 replace 部分替代 search 部分|
|STRCMP(str1,str2)|逐字符比较两个字符串的大小|
|SUBSTRING(str,position[,length])|从 str 的 position  (索引从 1 开始)开始取 length 个字符|
|LTRIM \| RTRIM(str) \| TRIM|去除前端/后端空格|

### 数学函数

|函数|用途|
|---|---|
|ABS(num)|获取绝对值|
|BIN(decimal_num)|十进制转二进制|
|HEX(decimal_num)|十进制转十六进制|
|CONV(num,from_base,to_base)|进制转换|
|CEILING(num)|向上取整，得到比 num 大的最小整数|
|FLOOR(num)|向下取整，得到比 num 小的最大整数|
|FORMAT(num,decimal_places)|保留小数位数|
|LEAST(num1,num2[,...])|获取最小值|
|MOD(numerator,denominator)|取余|
|RAND([seed])|返回一个范围 [0，1.0] 的随机浮点值，若指定一个整数参数作为种子值，则可以产生重复序列|

### 加密 / 系统函数
|函数|用途|
|---|---|
|USER()|查询用户(user@IP_address)|
|DATABASE()|查询当前使用的数据库名称|
|MD5(str)|为字符串计算出一个 MD5 32位的字符串，用于用户密码的加密|
|PASSWORD(str)|从原文密码 str 计算返回密码字符串，常用于对 MySQL 数据库的用户密码加密|

### 流程控制函数

|函数|用途|
|---|---|
|IF(expr1,expr2,expr3)|如果 expr1 为 True，则返回 expr2，</br>否则返回 expr3|
|IFNULL(expr1,expr2)|如果 expr1 不为 NULL，返回 expr1，</br>否则返回 expr2|
|SELECT CASE </br>WHEN expr1 THEN expr2</br>WHEN expr3 THEN expr4 </br>ELSE expr5 END;|如果 expr1 为 True，返回 expr2；</br>如果 expr3 为 True，返回 expr4；</br>否则返回 expr5。|

注意：
* 判断一个字段是否为空要用 IS NULL 而非 == NULL 。

## 连接
### 内连接
* INNER JOIN —— 取交集(A ∩ B)

### 外连接
* LEFT JOIN —— 左表记录全部显示
```
SELECT ... 
    FROM table1 LEFT JOIN table2
    ON where_definition;
```
* RIGHT JOIN —— 右表记录全部显示
```
SELECT ... 
    FROM table1 RIGHT JOIN table2
    ON where_definition;
```

## 约束
### not null(非空)
```field datatype NOT NULL```

插入数据时，必须为列提供数据，不能为 NULL

### primary key(主键)
```field datatype PRIMARY KEY```

用于唯一的标示行的数据，当定义主键约束后，该列不能重复

细节：
1. PRIMARY KEY 不能重复且不能为 NULL 。
2. 一张表最多只能有一个主键，但可以是复合主键。
3. 主键的指定方法：
    *  ```field datatype PRIMARY KEY```
    *  在表定义最后 ```PRIMARY KEY(column_name)```
4. 使用 ```DESC table_name``` 可以看到 PRIMARY KEY 的情况。

### unique(唯一)
```field datatype UNIQUE```

该列的值不能重复

细节：
1. 如果字段没有指定 NOT NULL ，则 UNIQUE 字段可以有多个 NULL 。
2. 如果字段同时指定了 NOT NULL 和 UNIQUE ，使用效果类似于 PRIMARY KEY 。
3. 一张表可以有多个 UNIQUE 字段。

### foreign key(外键)
```
FOREIGN KEY(col_name)REFERENCES
main_table_name(PRIMARY_KEY_name | UNIQUE_name)
```
</br>

用于定义主表和从表之间的关系：外键约束定义在从表上，主表则必须具有主键约束或唯一约束，当定义外键约束后，要求外键列数据必须在主表的主键列存在或是为 NULL 。

细节：
1. 外键指向的表的字段要求是 PRIMARY KEY 或 UNIQUE 。
2. INNODB 引擎才支持外键。
3. 外键字段的类型和主键字段类型要保持一致(长度可以不相等)。
4. 外键字段的值必须在主键字段中出现过，或者为 NULL (前提是外键字段允许为 NULL)。
5. 一旦建立外键关系就不能随意删除数据。

### check(检查)
```field datatype CHECK(check_rules)```

用于强制行数据必须满足的条件。

### 自增
```field INT PRIMARY KEY AUTO_INCREMENT```

细节：
1. 自增和主键配合使用。
2. 自增可以单独使用，但是需要配合 UNIQUE 。
3. 自增修饰的字段绝大多数使用整数类型。
4. 自增默认从 1 开始，修改方法为：</br>```ALTER TABLE table_name AUTO_INCREMENT = xxx;```
5. 如果添加数据时没有使用自增值而是使用了给定值，以给定值为准。且自增的起点改变。

## 索引
```CREATE INDEX index_name ON table_name(col_name);```

索引的原理：
* 无索引时，查询会进行全表扫描；添加索引后，会形成树结构。

索引的代价：
* 磁盘占用变大。
* 影响 DML 语句的效率(树结构会一定程度的重构)。

### 索引使用
添加索引

```CREATE [UNIQUE] INDEX index_name ON table_name(col_name[(length)][ASC|DESC],...);```

或

```ALTER TABLE table_name ADD INDEX[index_name](index_col_name,...);```

添加主键(索引)

```ALTER TABLE table_name ADD PRIMARY KEY(col_name,...);```

删除索引

```DROP INDEX index_name ON table_name;```

或

```ALTER TABLE table_name DROP INDEX index_name;```

删除主键索引

```ALTER TABLE table_name DROP PRIMARY KEY;```

查询表的索引情况

```SHOW INDEX(ES) | KEYS FROM table_name;```

### 索引的类型
* PRIMARY(主键索引)
    * 主键索引会自动成为索引
* UNIQUE(唯一索引)
    * 唯一约束会自动成为索引
* INDEX(普通索引)
* FULLTEXT(全文索引)

### 哪些列适合 / 不适合使用索引？
1. 频繁作为查询条件的字段。
2. 唯一性太差的不适合单独创建索引。
3. 更新频繁的字段不适合创建索引。
4. 不会在 WHERE 字句中出现的字段不适合创建索引。

## 事务
### 事务的概念
事务用于保证数据的一致性，它由一组相关的 DML 语句组成，该组语句要么全部成功，要么全部失败。

* 回退事务
    * 保存点是事物中的点，用于取消部分事物，当结束事物时，会自动删除该事务定义的所有保存点。
    * 执行回退事务时，通过指定保存点可以回退到指定的点。
* 提交事务
    * 使用 COMMIT 语句可以提交事务，提交后确认了事务的变化、结束事务、删除保存点、释放锁、数据生效。
    * 使用 COMMIT 语句结束事务后，其他会话可以查看到事务变化后到新数据。

### 事务的操作
|操作|含义|
|---|---|
|START TRANSACTION|开始一个事务|
|SAVEPOINT savepoint|设置保存点|
|ROLLBACK TO savepoint|回退事务|
|ROLLBACK|回退全部事务|
|COMMIT|提交事务，所有操作生效，无法回退|

### 事务的细节
1. 如果不开启事务，默认情况下 DML 语句自动提交，不能回滚。
2. 如果开始一个事务，没有创建保存点时执行 ROLLBACK 会回到事务开始状态。
3. 未提交前可以创建多个保存点。
4. 未提交前可以选择回退到任意保存点。
5. INNODB 存储引擎支持事务，MYISAM 不支持。
6. 开始一个事务：```START TRANSACTION``` 或 ```SET AUTOCOMMIT = OFF;```

### 事务的隔离级别
脏读(dirty read)：当一个事务读取另一个事务尚未提交的修改时，产生脏读。

不可重复读(nonrepeatable read)：同一查询在同一事务中多次进行，由于其他提交事务所做的修改或删除，每次返回不同的结果集，此时发生不可重复读。

幻读(phantom read)：同一查询在同一事务中多次进行，由于其他提交事务所做的插入操作，每次返回不同的结果集，此时发生幻读。

|MySQL 隔离级别|脏读|不可重复读|幻读|加锁读|
|---|---|---|---|---|
|读未提交(Read uncommitted)|√|√|√|不加锁|
|读已提交(Read committed)|X|√|√|不加锁|
|可重复读(Repeatable read)|X|X|X|不加锁|
|可串行化(Serializable)|X|X|X|加锁|

**MySQL 默认隔离级别为可重复读**

隔离级别常用指令
* 查看当前回话隔离级别</br>```SELECT @@tx_isolation;```
* 查看系统当前隔离级别</br>```SELECT @@global.tx_isolation;```
* 设置当前回话隔离级别</br>```SET SESSION TRANSACTION ISOLATION LEVEL level```
* 设置系统当前隔离级别</br>```SET GLOBAL TRANSACTION ISOLATION LEVEL level```

### 事务的 ACID
* 原子性(Atomicity)
    * 指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。
* 一致性(Consistency)
    * 事务必须时数据库从一个一致性状态转换到另一个一致性状态。
* 隔离性(Isolation)
    * 多个用户并发访问数据库时，数据库为每个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。
* 持久性(Durability)
    * 一个事务一旦被提交，它对数据库中的数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响。

## 存储引擎
* 事务安全型(Transaction-safe)
    * InnoDB
* 非事务安全型(Non-transaction-safe)
    * CSV、BLACKHILE、Memory、ARCHIVE、MRG、MYISAM

查看所有存储引擎的指令： ```SHOW ENGINES```

修改存储引擎：```ALTER TABLE table_name ENGINE = engine_name;```

### 主要存储引擎的特点

|特点|MYISAM|InnoDB|Memory|Archive|
|---|---|---|---|---|
|批量插入的速度|高|低|高|非常高|
|事务安全||支持|||
|全文索引|支持||||
|锁机制|表锁|行锁|表锁|行锁|
|存储限制|没有|64 TB|有|没有|
|B 树索引|支持|支持|支持||
|哈希索引||支持|支持||
|集群索引||支持|||
|数据缓存||支持|支持||
|索引缓存|支持|支持|支持||
|数据可压缩|支持|||支持|
|空间使用|低|高|N/A|非常低|
|内存使用|低|高|中等|低|
|支持外键||支持|||

细节说明：
1. MyISAM 不支持事务、外键，但是其访问速度快，对事务完整性没有要求。
2. InnoDB 提供了具有提交、回滚和崩溃恢复能力对事务安全。但是比起 MyISAM ，InnoDB 写的效率差一些并且会占用更多的磁盘空间以保留数据和索引。
3. MEMORY 使用存在内存中的内容来创建表。每个 MEMORY 表只实际对应一个磁盘文件。MEMORY 类型的表访问非常快，因为它的数据时存放在内存中的，并且默认使用哈希索引。一旦服务关闭，表结构存留，但表中的数据会丢失。

如何选择表的存储引擎？
1. 如果应用不需要事务，处理的只是基本的 CRUD 操作，那么建议和使用速度较快的 MyISAM 。
2. 如果应用需要事务，建议使用 InnoDB 。
3. Memory 存储引擎将数据存储在内存中，由于没有磁盘 I/O 的等待，速度极快。但由于时内存存储引擎，所做的任何修改在服务器重启后都将消失。(经典用法：**用户的在线状态**)

## 视图 (View)
### 基本概念
1. 视图是根据一个或多个基表来创建的，视图是虚拟的表。
2. 视图包含列，其数据来自对应基表。
3. 通过视图可以修改基表的数据；基表的改变也会影响视图。

### 视图的基本使用
创建视图

```CREATE VIEW view_name AS select_sentence```

修改视图

```ALTER VIEW view_name AS select_sentence```

查询视图创建时的指令

```SHOW CREATE VIEW view_name```

删除视图

```DROP VIEW view_name1,view_name2...```

### 使用细节
1. 创建视图后，在数据库中查看时，对应视图只有一个视图结构文件(view_name.frm) ，没有数据文件。
2. 通过视图可以修改基表的数据；基表的改变也会影响视图。
3. 视图中可以再使用视图。

### 视图的最佳实践
1. 安全
    * 一些数据表有着重要的信息，有些字段是保密的，不能让用户直接看到。这时候就可以创建一个视图，在这张视图中只保留一部分字段。这样，用户就可以查询自己需要的字段，不能查看保密字段。
2. 性能
    * 关系数据库的数据常常会分表存储，使用外键建立这些表之间的关系。这时，数据库查询通常会用到连接 (JOIN) 。这样做不但麻烦，效率也相对较低。如果建立一个视图，将相关的表和字段组合在一起，就可以避免使用 JOIN 查询数据。
3. 灵活
    * 如果系统中有一张旧表，这张表由于设计问题即将被废弃。然而很多应用都是基于这张表，不易修改。这时就可以建立一张视图，视图中的数据直接映射到新键的表。这样就可以少做很多改动，同时达到了升级数据表的目的。

## 权限管理
### 用户管理
MySQL 中的用户都存储在系统数据库中的 user 表里。

user 表的重要字段说明：
1. host：允许登陆的"位置"，localhost 表示该用户只允许本机登陆，也可以指定 IP 地址。
2. user：用户名。
3. authentication_string：密码，通过 MySQL 的 PASSWORD()函数加密后的密码。

创建用户

```CREATE USER `username`@`允许登陆位置` IDENTIFED BY `password`;```

删除用户

```DROP USER `username`@`允许登陆位置`;```

修改自己的密码

```SET PASSWORD = PASSWORD(`password`)；```

修改他人的密码(需要由修改用户密码权限)

```SET PASSWORD FOR `username`@`允许登陆位置` = PASSWORD(`password`);```

### 权限管理
给用户授权

```GRANT 权限列表 ON database.对象名 TO `username`@`允许登陆位置`[IDENTIFIED BY `password`]```

回收用户权限

```REVOKE 权限列表 ON database.对象名 FROM `username`@`允许登陆位置`;```

权限生效指令

如果权限没有生效，可以执行这句指令：```FLUSH PRIVILEGES```

说明
1. 权限列表，多个权限用逗号分隔。</br>```GRANT SELECT ON ...```</br>```GRANT SELECT,DELETE,CREATE ON ...```</br>```GRANT ALL [PRIVILEGES] ON ... -- 表示赋予该用户在该对象上的所有权限```
2. 特别说明：</br>\* . \* ：代表本系统中当所有数据库当所有对象(表，视图，存储过程)</br>database . * ：表示某个数据库中的所有数据对象(表，视图，存储过程等)
3. IDENTIFIED BY 可以省略
    1. 如果用户存在则修改他的密码
    2. 如果用户不存在则创建该用户

细节：
1. 创建用户的时候，如果不指定 Host，则为 % 。% 表示所有 IP 都有连接权限。
2. 指定 IP 地址时可以使用形如 `192.168.1.%` 的 IP 段的格式。
3. 删除用户时，如果 Host 不是 % ，需要明确指定用户的登陆 IP 地址。