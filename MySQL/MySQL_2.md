# 数据库

# 1. MySQL 数据表的设计

## 1.1 MySQL 建表语句

```mysql
# 建表语句
CREATE TABLE [IF NOT EXISTS] `表名`(
    `字段名1` 数据类型 [字段属性],
    `字段名2` 数据类型 [字段属性],
    ...
    `字段名N` 数据类型 [字段属性]
)[表引擎=引擎名] [DEFAULT CHARSET=utf8mb4];
```

## 1.2 建表语句的含义与注意事项

1. 表名反引号，可区别屏蔽 系统关键字
2. 表引擎，详见 `1.3 表引擎说明`
    - ENGINE = MyISAM
    - ENGINE = InnoDB [默认,推荐]
3. 字符集 `DEFAULT CHARSET=utf8`，常见值为
    - `utf8` Unicode 字符集
    - `gbk` 文字编码是用双字节来表示
    - `utf8mb4` （推荐）更大的字符集，存储生僻字，颜文字表情
4. 字段名加上反引号，字段之间用逗号分隔。
5. 最后一个字段的后面，不能有逗号。
6. 字段名要有相应的字段类型，也就是数据类型，字段属性可选。

## 1.3 MySQL 表引擎说明

> MySQL的引擎有很多, 下面只介绍两个常用的：`MyISAM` 和 `InnoDB`

|  | MyISAM | InnoDB |
| :- | :- | :- |
| 事务 | No | Yes |
| 全文索引 | Yes | No （可用 Sphinx 代替） |
| 表总行数 | 存储 <br>count() 可直接获取 <br>count() 若带where条件, 则会遍历整表, 消耗巨大 | 未存储 <br>当场遍历整表, 消耗巨大 |
| CURD | 擅长 R <br>查询数据 | 擅长 C、U、D <br>增、删、改 |
| 行表锁 | 表锁 <br>开销小, 加锁快, 低并发 | 行锁 (默认) , 表锁 <br>开销大, 加锁慢, 易死锁, 高并发 |

## 1.4 MySQL 字段属性说明

| 字段属性         | 属性名   | 描述 |
| ----            | ----    | ---- |
| UNSIGNED        | 无符号   | 没有正负号，即没有负数 |
| AUTO_INCREMENT  | 自增     | 每新增一条数据，该字段值 +1<br>**根据记录 +1，而非已有最大值 +1**<br>不允许重复<br>常配合主键使用 |
| ZEROFILL        | 零填充   | 显示宽度不足时，高位用 0 代替<br>**自动加上 unsigned 属性** |
| DEFAULT         | 默认值   | 没有给该字段赋值时，则自动采用默认值 |
| NOT NULL        | 非空约束 | 该字段必须有值 |
| NULL            | 空约束   | 该字段可以不赋值 |
| COMMENT         | 注释描述 | 描述字段的功能（常用于：开关/状态的说明） |
| PRIMARY KEY     | 主键索引 | 主键数据**不能为空**<br>主键数据**不能重复**<br>主键**不能重复设置**<br>常配合自增使用 |
| UNIQUE          | 唯一索引 | 索引数据**不能重复**<br>索引**可以设置多个**<br>索引可以**提高查询速度**<br>占用磁盘空间<br>索引**不宜太多**（消耗空间，消耗性能） |

> 表内最后两项为`索引类型`
>
> 以上的 字段属性 和 索引类型 会在后续示例中讲解。

----

# 2. MySQL 数据类型

## 2.0 数据类型说明

> MySQL 中常用的数据类型主要有四种，见下表

| 数据类型           | 说明                                 |
| ------------------ | ------------------------------------ |
| 数值数据类型       | 支持多种数值类型，且取值范围不同：整型、浮点型 |
| 串数据类型         | 最常用，多用于：定长字串、变长字串 |
| 日期和时间数据类型 | 具有格式化的：时间、日期 |
| 二进制数据类型     | 可存储任何数据，包括二进制信息：图像，音/视频，文档等 |

## 2.1 整型

| 类型 | 字节 | 无符号范围 | 有符号的范围 | 数量级说明 |
| ---- | :--: | ---- | ---- | ---- |
| TINYINT | 1 | 0 ~ 255 | -128 ~ +127 | 百级别 |
| SMALLINT | 2 | 0 ~ 65535 | -32768 ~ +32767 |万级别|
| MEDIUMINT | 3 | 0 ~ 16777215 | -8388608 ~ +8388607 |千万级别|
| INT | 4 | 0 ~ 4294967295 | -2147483648 ~ +2147483647 |几十亿级别|
| BIGINT | 8 | 0 ~ 18446744073709551615 | -9223372036854775808 ~<br>+9223372036854775807 |千G级别|

### 2.1.1 类型规则

- 不同的整型类型`占用空间`是不同的。
- 输入值在合理范围内，不论值是多少，都占同样的存储空间。
- 表示范围，默认都是`有符号的`表示范围。
- `无符号`由字段属性 `UNSIGNED` 来决定。

```mysql
# 测试整数的范围,及`unsigned`的使用
# 创建`test0`表
CREATE TABLE `test0`(
    `num` TINYINT
)engine=InnoDB default charset=utf8mb4;

# 创建好之后 查看表结构
DESC `test0`;

# 创建`test1`表
CREATE TABLE `test1`(
    `num` TINYINT UNSIGNED
)engine=InnoDB default charset=utf8mb4;

# 创建好之后 查看表结构
DESC `test1`;

# 添加 测试数据
INSERT INTO `test0` VALUES(50);
INSERT INTO `test0` VALUES(-30);
INSERT INTO `test0` VALUES(-300);
INSERT INTO `test0` VALUES(300);

INSERT INTO `test1` VALUES(50);
INSERT INTO `test1` VALUES(-30);
INSERT INTO `test1` VALUES(300);

# 查询测试以上数据
```

> PS. 警告提示，根据实际情况修改一下参数。

```ini
5.7 版本的 MySQL 对字段值 会严格检查：
插入越界值、某些非空值、插入时没有给值，都会报错！！！

可更改`my.ini`配置文件，并重启服务
将`sql-mode`注释，并改为以下:
sql-mode = "NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"

修改后,重新登录 mysql 命令行 才可以继续操作
```

> 我们为了节省空间 和 提高数据处理的效率。
> 就要根据 具体的应用数据，来设定相应的数据类型。

### 2.1.2 显示宽度 与 零填充

> `整数类型(n)` ：n - 显示宽度    如: INT(11)

- 显示宽度可以与`ZEROFILL 零填充`配合使用。//补宽度
    - 如果加上此属性，字段内会自动加上`UNSIGNED`属性。
- 显示宽度与存储字节`无关`。
    - 插入的数据宽度`大于`设置的宽度，并在有效范围内，依旧会插入成功。
    - 插入的数值`范围超限`，则会报错。

```mysql
# 测试 显示宽度 / 零填充 ZEROFILL
# 创建`test2`表
CREATE TABLE `test2`(
    `num` INT(5) ZEROFILL
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# 建表，并查看表结构
DESC `test2`;

# 测试数据
INSERT INTO `test2` VALUES(300);
INSERT INTO `test2` VALUES(8);

# 查询测试以上数据
SELECT * FROM test2;
+-------+
| num   |
+-------+
| 00300 |
| 00008 |
+-------+
2 rows in set (0.00 sec)

# 插入超过显示宽度的合理范围值
INSERT INTO `test2` VALUES(88888888);

# 查询测试以上数据
```

### 2.1.3 自增 与 主键索引

- 主键索引：`PRIMARY KEY`
    - 一个表内只能有一个字段使用 AUTO_INCREMENT，且该字段必须为主键。
    - 主键的值 `不能重复`。
    - 数据表会根据`主键的唯一性`，来唯一标识每一条记录。
    - 该字段的值，默认是从`1`开始自增的。
    - 该字段的值不能为空。一般配合整型一起使用。
- 自增：`AUTO_INCREMENT`
    - 创建表时只定义自增，会报错！
    - 自增字段，必须要配合`PRIMARY KEY`使用。
    - 自增时，如果指定数字，必须比前面的索引数要大，否则不生效

```mysql
# 测试 `auto_increment`(自增) / `primary key`(主键)
# 创建`test3`表
CREATE TABLE `test3`(
    `id` INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    `name` VARCHAR(255)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# 查看表结构
DESC `test3`;

+-------+------------------+------+-----+---------+----------------+
| Field | Type             | Null | Key | Default | Extra          |
+-------+------------------+------+-----+---------+----------------+
| id    | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
| name  | varchar(255)     | YES  |     | NULL    |                |
+-------+------------------+------+-----+---------+----------------+
2 rows in set (0.00 sec)

# 插入测试数据
# 可以 不用写 id 值,因为是自增,也可以填入 null
INSERT INTO `test3` (`name`) VALUES
('user1'), ('user2'), ('user3'), ('user4'),
('user5'), ('user6'), ('user7'), ('user8'),
('user9'), ('user10');

# 查看数据
SELECT * FROM `test3`;

# id 自增且连续
+----+--------+
| id | name   |
+----+--------+
|  1 | user1  |
|  2 | user2  |
|  3 | user3  |
|  4 | user4  |
|  5 | user5  |
|  6 | user6  |
|  7 | user7  |
|  8 | user8  |
|  9 | user9  |
| 10 | user10 |
+----+--------+
10 rows in set (0.00 sec)

# 删除全部数据
DELETE FROM `test3`;

# 查询 test3 数据为空
# 再插入一条数据
INSERT INTO `test3` (`name`) VALUES ('伊利丹');

# 查看数据 ID 为 11
SELECT * FROM `test3`;

# 查询当前最大ID
select last_insert_id();
```

PS. 自增归零的三种方式：
- TRUNCATE TABLE 表名; # 清空数据表
- 直接重置`auto_Increment`的值 (对 MyISAM 引擎起效)
    - `ALTER TABLE 表名 AUTO_INCREMENT = 1;`
- 删表，调整数据，重新构建.

> 不建议重置 主键的自增.

----

## 2.2 浮点型

| 类型    | 字节                | 范围（有符号）                                               | 范围（无符号）                                               |
| ------- | :-----------------: | ------------------------------------------------------------ | ------------------------------------------------------------ |
| FLOAT   | 4                   | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38)                  |
| DOUBLE  | 8                   | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) |
| DECIMAL(M,D) | M>D则M+2 否则D+2 | 依赖于M和D的值                                               | 依赖于M和D的值                                               |

### 2.2.1 类型规则

- 不同的浮点类型`占用空间`是不同的。
- 输入值在合理范围内，不论值是多少，都占同样的存储空间。
- 表示范围，默认都是`有符号的`表示范围。
- `无符号`由字段属性 `UNSIGNED` 来决定。

### 2.2.2 使用说明

不管是`单精度浮点数(FLOAT)`还是`双精度浮点数(DOUBLE)`
通常是用来保存一些数量特别大，大到可以不用那么精确的数据。

> 如果要更精确的数据，请务必使用`定点数(DECIMAL)`。

```mysql
DECIMAL(M,D)
    M - 精度，是数据的总长度，小数点不占用长度
    D - 标度，小数点后的长度
# 我们一般使用定点数`DECIMAL`来表示小数
# 占有长度：如果 M>D 则为 M+2; 否则为 D+2

# 测试 decimal
# 创建`test4`表
CREATE TABLE `test4`(
    `price` DECIMAL(10,2)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# 查看表结构
DESC `test4`;

# 测试数据
INSERT INTO `test4` VALUES('100');
INSERT INTO `test4` VALUES('12345678');
INSERT INTO `test4` VALUES('998.9');
# 超范围数值
INSERT INTO `test4` VALUES('123456789');
# 超标度
INSERT INTO `test4` VALUES('123.456');
INSERT INTO `test4` VALUES('123.999');

# 查询测试以上数据
```

----

## 2.3 字符串类型

| 类型       | 大小              | 用途                            |
| :--------- | ----------------- | :------------------------------- |
| CHAR       | 0 - 255 字节        | 定长字符串                      |
| VARCHAR    | 0 - 65535 字节      | 变长字符串                      |
| TINYTEXT   | 0 - 255 字节        | 短文本字符串                    |
| TEXT       | 0 - 65535 字节      | 长文本数据                      |
| MEDIUMTEXT | 0 - 16777215 字节   | 中等长度文本数据                |
| LONGTEXT   | 0 - 4294967295 字节 | 极大文本数据                    |

----

### 2.3.1 CHAR 与 VARCHAR

- `CHAR(n)`    固定长度 字符类型
    - 指定`n`为多少 就占用多少空间
    - 能够使用 char 的时候，尽量使用 char
- `VARCHAR(n)` 可变长度 字符类型
    - 占用空间 根据实际长度 `n+1`
    - `n`:
        - 5.0 版本以前 长度范围是 `0~255`;
        - 5.0 版本以后 长度范围是 `0~65535`;
        - 注意 utf8 字符集，VARCHAR 的长度会缩水，详见 : [说明](https://www.cnblogs.com/gomysql/p/3615897.html)

> CHAR(5) 和 VARCHAR(5) 的区别

|  插入值  | CHAR 占用字节数 | VARCHAR 占用字节数 |
| :------: | :-------------: | :----------------: |
|    ''    |        5        |         1          |
|   '1'    |        5        |         2          |
|  '123'   |        5        |         4          |
| '12345'  |        5        |         6          |
| '123456' |        5        |         6          |

以上表中，最后一行中 多余的字符会被截取。
如果插入值的长度 大于(n)，只插入限定的长度部分，其余会被截取。

----

```mysql
# 测试 varchar 和 char
# 创建`test5`表
# name 唯一不可重复，且密码定长不为空
CREATE TABLE `test5`(
    `name` VARCHAR(255) UNIQUE,
    `pwd` CHAR(32) NOT NULL
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# UNIQUE 唯一索引,一个表内 可以有多个 唯一索引.
# NOT NULL 值不得为空

# 查看表结构
DESC `test5`;

+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| name  | varchar(255) | YES  | UNI | NULL    |       |
| pwd   | char(32)     | NO   |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
2 rows in set

# 插入测试数据
# md5() 为 mysql 的加密函数，可加密成 32 位的字串
INSERT INTO `test5` (`name`,`pwd`) VALUES('马小云', md5('123456'));
INSERT INTO `test5` (`name`,`pwd`) VALUES('seeker', md5('123456'));

# 查询测试以上数据
```

### 2.3.2 TEXT 类型

2.3 表中，除了 CHAR 与 VARCHAR 以外的类型，一般都认为是 TEXT 类型。
占用空间 一般是 `文本长度+2字节`
用于表示大段的文本。如：小说、网页源码 等等

----

### 2.3.3 ENUM 类型

ENUM 类型，又称`枚举类型`，属于串类型，功能是在多个值之间选一个值。

> 百里挑一

```mysql
ENUM('值1', '值2', ..., '值N')
    ENUM 类型的值 只能获取列表中的 一个元素
    取值列表中 最多能有`65535字节`组成成员

# 测试 ENUM
# 创建`test6`表
CREATE TABLE `test6`(
    `sex` ENUM('man','woman','two-in-one')
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# 查看表结构
DESC `test6`;

# 测试数据
INSERT INTO `test6` VALUES('man');
INSERT INTO `test6` VALUES('woman');
INSERT INTO `test6` VALUES('two-in-one');
INSERT INTO `test6` VALUES('hehe');

# 查询测试以上数据
```

### 2.3.4 SET 类型

SET 类型，属于串类型，功能是在多个值之间选一个获多个值。

> 百里挑N

```mysql
SET('值1', '值2', ..., '值n')
    取值可以是 其中的一个或多个
    SET 类型的值 最多能有`65535字节`组成成员

# 测试 SET
# 创建`test7`表
CREATE TABLE `test7`(
    `hobby` SET('man','woman','ps','ml','knvb')
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# 查看表结构
DESC `test7`;

# 测试数据  不存在的值 就添加不到表里
INSERT INTO `test7` VALUES('man,woman,ps');
INSERT INTO `test7` VALUES('woman,knvb,haha');

# 查询测试以上数据
```

----

## 2.4 日期和时间型

| 日期类型  | 字节 | 日期格式            | 最小值     | 最大值     | 零值       |
| :-------: | :--: | ------------------- | ---------- | ---------- | ---------- |
|   DATE    |  4   | YYYY-MM-DD          | 1000-01-01 | 9999-12-31 | 0000-00-00 |
|   TIME    |  3   | HH:MM:SS            | 00:00:00   | 23:59:59   | 00:00:00   |
|   YEAR    |  1   | YYYY                | 1901       | 2155       | 0000       |
| DATETIME  |  4   | DATE 和 TIME 的组合 | ---        | ---        | ---        |
| TIMESTAMP |  8   | 功能与DATETIME相同  | 但范围较小 | ---        | ---        |

> 每个时间类型都有 一个  有效值范围 和 一个 "零"值
>
> 当指定不合法的 MySQL 不能表示的值时使用"零"值。
>
> > `TIMESTAMP` :
> > 结束时间是第 `2147483647` 秒,
> > 北京时间 `2038-1-19 11:14:07`，格林尼治时间 `2038-1-19 03:14:07`

----

## 2.5 二进制数据

| 类型       | 大小              | 用途                            |
| :--------- | ----------------- | :------------------------------- |
| TINYBLOB   | 0 - 255 字节        | 不超过 255 个字符的二进制字符串 |
| BLOB       | 0 - 65535 字节      | 二进制形式的长文本数据 （最大为64KB） |
| MEDIUMBLOB | 0 - 16777215 字节    | 二进制形式的中等长度文本数据（最大为16MB） |
| LONGBLOB   | 0 - 4294967295 字节 | 二进制形式的极大文本数据（最大为4GB） |

> 在数据库中存储二进制数据的数据类型,类似于 text
> 如图像、音视频、各类文档等等

## 2.6 DEFAULT 默认值

> 详见 1.4 部分的表格说明

```mysql
# 测试 DEFAULT
# 创建`test8`表
CREATE TABLE `test8`(
    `str` VARCHAR(255) NOT NULL DEFAULT 'haha',
    `str1` VARCHAR(255)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# 查看表结构
DESC `test8`;

# 测试数据 只指定str1的值,不提供str字段值 就是用默认值
INSERT INTO `test8`(`str1`) VALUES('a');
INSERT INTO `test8`(`str1`) VALUES('b');
INSERT INTO `test8`(`str`,`str1`) VALUES('GG','c');

# 查询测试以上数据
```

----

# 3. MySQL 索引

| 关键字      | 索引说明 |
| -----------: | -------- |
| PRIMARY KEY | 主键索引 |
| UNIQUE      | 唯一索引 |
| INDEX       | 常规索引（普通索引） |
| FULLTEXT    | 全文索引 （sphinx）|

> 可参见 1.4 部分的表格说明

----

# 4. 表属性的操作

```mysql
# 4.1 修改表名
ALTER TABLE `旧表名` RENAME AS `新表名`;


# 4.2 添加字段
ALTER TABLE `表名` ADD `字段名` 数据类型 字段属性;


# 4.3 修改字段属性
ALTER TABLE `表名` MODIFY `字段名` 新数据类型;


# 4.4 修改字段名 及 字段属性
ALTER TABLE `表名` CHANGE `旧字段名` `新字段名` 新数据类型;


# 4.5 删除字段
ALTER TABLE `表名` DROP `字段名`;


# 4.6 更改表的存储引擎
# 查看MYSQL有那些表引擎
SHOW ENGINES\G
# 查看指定数据表的引擎
SHOW CREATE TABLE `表名`;

ALTER TABLE `表名` ENGINE='存储引擎名称';
```
