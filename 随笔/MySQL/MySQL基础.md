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
2. 字符串和日期类型需要用单引号 '' 包裹
3. 字段允许 null 时，允许插入空值
4. 当不给某个字段插入值时，如果有默认值会默认添加，否则会报错；如果某个列没有指定 not null，那么当插入数据时不给该字段添加值，默认添加 NULL。

### Update


### Delete


### Select


## 函数
### 统计函数
### 时间日期
### 字符串函数
### 数学函数
### 流程控制
## 内连接
## 外连接
## 约束
### not null
### primary key
### unique
### foreign key
### check
### 自增
## 索引
### 主键索引
### 唯一索引
### 普通索引
### 全文索引
## 事务