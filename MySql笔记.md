# MySql 笔记

## 软件：
### 1，从docker进入：
```shell
    docker run <docker-container-name> mysql -n <user-name> -p
```
如果已经创建过docker
```shell
    docker exec -it <docker-container-name>  mysql -n <user-name> -p
```
	 
### 2，从系统进入：
```shell
    mysql -n <user-name> -p
```

    mysql：软件名
	-n: 使用用户名称登录
	-p: 使用用户密码登录

## 数据库：
### 1，查看数据库：
```sql
	SHOW DATABASES;
```
### 2，创建数据库：
```sql
	CREATE DATABASE <database-name>;
```
### 3，删除数据库：
```sql
	DROP DATABASE <database-name>;
```

## 列表操作：
### 1，指定要操作的列表：
```sql
	USE <table-name>;
```
### 2，规定列表的表头和元素的类型：
```sql
CREATE TABLE <table-name> {
    <element1-name> <element1-type>,
    <element2-name> <element2-type>,
    <element3-name> <element3-type>,
    ...
    <elementn-name> <elementn-type>
}
```
例如：
```sql

CREATE TABLE player(
    id INT,
    name VARCHAR(100),
    level INT,
    exp INT,
    gold DECIMAL(10,2)
);
```


[点击查看有哪些数据类型](#数据类型总表)
### 3，描述表的结构：
```sql
    DESC <table-name>;
```

### 4，更改表的数据类型：
```sql
    ALTER TABLE <table-name> MODIFY <element-name> <element-later-type>;
```
### 5，更改表的默认数据：
```sql
    ALTER TABLE <table-name> MODIFY <element-name> <element-type> DEFAULT <element-default-value>;
```
### 6，更改表的数据名称：
```sql
    ALTER TABLE <table-name> RENAME COLUMN <element-name> TO <element-later-name>;
```
### 7，在列表的结尾添加行：
```sql
    ALTER TABLE <table-name> ADD COLUMN <element-name> <element-type>;
```
### 8，列表删除行：
```sql
    ALTER TABLE <table-name> DROP COLUMN <element-name>;
```
### 9，删除列表：
```sql
    DROP TABLE <table-name>;
```

### 10,保存列表:
```shell
    mysqldump -uroot -p <table-name> > <file-name>
```

### 11,加载列表:
```shell
    mysql -uroot -p <table-name> < <file-name>
```



## 表的数据操作：
### 1，插入数据
```sql
    INSERT INTO <table-name> (<element1-name>,<element2-name>,...,<elementn-name>) VALUES (<element1-value>,<element2-value>,...,<elementn-value>)；
```
如果元素的顺序和表头的顺序一致,可以省略表头
```sql
    INSERT INTO <table-name>  VALUES (<element1-value>,<element2-value>,...,<elementn-value>)；
```
如果要插入很多数据,将数据放在 values 后面,逗号隔开
```sql
    INSERT INTO <table-name>  VALUES (<element11-value>,<element12-value>,...,<element1n-value>) , (<element21-value>,<element22-value>,...,<element2n-value>) , (<element31-value>,<element32-value>,...,<element3n-value>)；
```

### 2.查看数据
```sql
    SELECT * FROM <table-name>
```
如果想要查看某一列的数据
```sql
    SELECT <column-name> FROM <table-name>
```

### 3.修改数据
```sql
    UPDATE <table-name> SET <element-name> = <element-value>;
```

### 4.删除数据
```sql
    DELETE FROM <table-name>
```

## 子句：
### 1,WHERE 子句 
用于限定操作的范围，可以搭配SELECT,UPDATE,DELETE 一起使用
```sql
    SELECT <column-name> FROM <table-name> WHERE  <element-name> = <element-value> ;
```
### 2,条件运算符 
```sql
    SELECT * FROM player  WHERE id=10; --在玩家中查找 id 为10的单位
    SELECT * FROM player  WHERE id=10 AND exp<15; --在玩家中查找 id 为10且经验小于15的单位
    SELECT * FROM player  WHERE id=10 OR exp<15; --在玩家中查找 id 为10或经验小于15的单位

    SELECT * FROM player  WHERE (id=10  OR exp<15) AND level=1;--在等级为一的玩家中查找 id 为10或经验小于15的单位，由于条件运算符优先级NOT>AND>OR，需要把OR的两端括起来
    SELECT * FROM player  WHERE id IN (1,2);--查找 id 为1和2的玩家
    SELECT * FROM player WHERE id BETWEEN 1 AND 10;--查找 id 为1到10的玩家
    SELECT * FROM player WHERE id NOT BETWEEN 1 AND 10;--与上一条相反，查找 id 不在1到10的玩家
```
### 3,模糊搜索
LIKE
```sql
    SELECT * FROM player  WHERE name LIKE 'a%';--%表示任意字符,可以是abc,abcd,也可以只是a
    SELECT * FROM player  WHERE name LIKE 'a_';--_表示一个字符,只能是ab,ac,ad
    --
```
REGEXP,正则表达式,[点击查看正则表达式规范](#正则表达式规范)
```sql
    SELECT * FROM player WHERE name REGEXP 'a';--查找名字中包含 a 的玩家
```
### 4,排序
ORDER BY 是排序，默认升序排列，多个排序条件时，就要用逗号隔开
```sql
    SELECT * FROM player ORDER BY id;--升序排列
    SELECT * FROM player ORDER BY id DESC;--降序排列
    SELECT * FROM player ORDER BY id DESC,exp;--先用id降序排列,id相同时,按照exp升序排列
    SELECT * FROM player ORDER BY 1;--也可以不用列名，而是使用列名所在的索引
```

### 5,聚合函数：
[点击查看有哪些聚合函数](#聚合函数大全)
```sql
    SELECT COUNT(*) FROM player;
```
### 6,分组：
```sql
    SELECT level,COUNT(*) FROM player GROUP BY level;--查找每个等级的玩家都有多少名
```
### 7,过滤：
```sql
    SELECT level,COUNT(*) FROM player GROUP BY level HAVING COUNT(level) > 2; --查找每个玩家数量大于2的等级中,玩家都有多少名
```
### 8,操作查询结果：
```sql
    SELECT *  FROM  player 
    UNION   
    SELECT * FROM player ORDER BY id;--取两条查询结果的并集,会去除重复元素
    SELECT *  FROM  player 
    UNION ALL
    SELECT * FROM player ORDER BY id;--取两条查询结果的并集,不会去除重复元素
    SELECT *  FROM  player
    INTERSECT
    SELECT * FROM player ORDER BY id;--取两条查询结果的交集
    SELECT *  FROM  player
    EXCEPT
    SELECT * FROM player ORDER BY id;--取两条查询结果的差集
```
 


## 子查询：
当想要把查询结果作为另一个查询的输入时，就需要用到子查询

```sql
    SELECT AVG(id) FROM player;--查询玩家的平均 id
    SELECT * FROM player WHERE id >(SELECT AVG(id) FROM player);--查询id大于玩家平均 id的单位
    SELECT id,((SELECT AVG(id) FROM player)),id-((SELECT AVG(id) FROM player)) FROM player;--查询 id,id 的均值,id 与均值的差值
    SELECT id,((SELECT AVG(id) FROM player)),id-((SELECT AVG(id) FROM player)) FROM player;--查询 id,id 的均值,id 与均值的差值
    SELECT id,((SELECT AVG(id) FROM player)) as avg,id-((SELECT AVG(id) FROM player)) as diff FROM player;--与上一条等价, 只是起了个别名
    CREATE TABLE now_table SELECT * FROM player WHERE id<10--创建一个新表,内容为上一个表的id小于十的那一部分
```
## 表关联：
```sql
    SELECT * FROM player--显示玩家表
    INNER JOIN equip--关联装备表
    --LEFT JOIN左连接，如果右表中没有匹配为数据，则返回空
    --RIGHT JOIN右连接，如果左表中没有匹配为数据，则返回空
    ON player.id = equip.player_id--条件为玩家 id
    --将玩家表和装倍表关联起来
```


## 索引
```sql
    CREATE INDEX gold_index on player(gold) --创建索引
    SHOW INDEX FROM player;--显示索引
    DROP INDEX gold_index ON player;--显示索引
    DROP INDEX gold_index ON player;--显示索引
    ALTER TABLE player ADD INDEX name_index (name);--创建表时添加索引

```

## 视图
```sql

```

## 易错点：

### 1,查找值为空的格子
查找值为空的格子时，必须使用 value is null
```sql
SELECT * FROM player WHERE name = null --×
SELECT * FROM player WHERE name is null --√
SELECT * FROM player WHERE name <=> null --×
```




# ***MySQL 数据类型总表***{#数据类型总表}

| **类别**       | **数据类型**      | **字节/长度**       | **范围或格式**                     | **中文说明**         | **典型用途**               |
|----------------|------------------|---------------------|------------------------------------|----------------------|----------------------------|
| **整数类型**   | `TINYINT`        | 1                   | -128~127 (有符号) / 0~255 (无符号) | 微小整数             | 状态码、年龄               |
|                | `SMALLINT`       | 2                   | -32768~32767 / 0~65535             | 小整数               | 年份、数量                 |
|                | `MEDIUMINT`      | 3                   | -838万~838万 / 0~1677万            | 中等整数             | 用户ID、订单号             |
|                | `INT`/`INTEGER`  | 4                   | -21亿~21亿 / 0~42亿                | 标准整数             | 主键、计数器               |
|                | `BIGINT`         | 8                   | -2⁶³~2⁶³-1 / 0~2⁶⁴-1               | 大整数               | 大数据量ID                 |
| **浮点数**     | `FLOAT`          | 4                   | 约±3.4E+38 (7位精度)               | 单精度浮点数         | 科学计算、地理坐标         |
|                | `DOUBLE`         | 8                   | 约±1.8E+308 (15位精度)             | 双精度浮点数         | 高精度计算                 |
|                | `DECIMAL(M,D)`   | 变长                | 精确值，M为总位数，D为小数位       | 定点数               | 货币、财务数据             |
| **位类型**     | `BIT(M)`         | 1~8 (M≤64)          | 二进制位（如 `b'1010'`）           | 位字段               | 标志位、权限开关           |
| **日期时间**   | `DATE`           | 3                   | `1000-01-01` ~ `9999-12-31`        | 日期                 | 生日、注册日期             |
|                | `TIME`           | 3                   | `-838:59:59` ~ `838:59:59`         | 时间                 | 持续时间、计时             |
|                | `YEAR`           | 1                   | `1901` ~ `2155`                    | 年份                 | 毕业年份                   |
|                | `DATETIME`       | 8                   | `1000-01-01 00:00:00` ~ `9999-12-31 23:59:59` | 日期时间       | 创建时间、日志记录         |
|                | `TIMESTAMP`      | 4                   | `1970-01-01 00:00:01` ~ `2038-01-19 03:14:07` | 时间戳       | 自动更新时间、国际化       |
| **字符串**     | `CHAR(M)`        | 0~255               | 定长字符串                         | 定长文本             | 邮编、固定长度编码         |
|                | `VARCHAR(M)`     | 0~65535             | 变长字符串                         | 变长文本             | 用户名、地址               |
| **长文本**     | `TINYTEXT`       | 0~255字节           | 短文本                             | 短文本               | 简介、备注                 |
|                | `TEXT`           | 0~64KB              | 长文本                             | 标准文本             | 文章内容                   |
|                | `MEDIUMTEXT`     | 0~16MB              | 中等文本                           | 中等文本             | 电子书章节                 |
|                | `LONGTEXT`       | 0~4GB               | 超长文本                           | 超长文本             | 日志文件、大型文档         |
| **二进制**     | `BLOB`           | 0~64KB              | 二进制数据                         | 二进制对象           | 图片、PDF                  |
|                | `LONGBLOB`       | 0~4GB               | 超大二进制数据                     | 超大二进制对象       | 视频、音频                 |
| **枚举集合**   | `ENUM('v1','v2')`| 1~2字节             | 单选值（如 `'男'/'女'`）           | 枚举类型             | 性别、状态选项             |
|                | `SET('a','b')`   | 1~8字节             | 多选值（如 `'a,b'`）               | 集合类型             | 标签、多选属性             |
| **其他**       | `JSON`           | 变长                | JSON格式数据                       | JSON数据             | 配置、结构化数据           |
|                | `GEOMETRY`       | 变长                | 空间数据（点、线、面）             | 几何类型             | 地图坐标、GIS系统          |

# ***正则表达式规范***{#正则表达式规范}

| 类别         | 元字符/语法       | 说明                                                                 | 示例                      |
|--------------|-------------------|----------------------------------------------------------------------|---------------------------|
| **基本匹配** | `普通字符`        | 直接匹配字符本身                                                     | `abc` 匹配 "abc"          |
| **字符类**   | `[abc]`           | 匹配方括号内的任意单个字符                                           | `[aeiou]` 匹配 "a"或"e"   |
|              | `[^abc]`          | 匹配不在方括号内的任意字符                                           | `[^0-9]` 匹配非数字       |
|              | `[a-z]`           | 字符范围（匹配a到z之间任意字符）                                     | `[A-Z]` 匹配大写字母      |
| **预定义类** | `.`               | 匹配除换行符外的任意单个字符                                         | `a.c` 匹配 "abc"、"a c"   |
|              | `\d`              | 匹配数字（等价于`[0-9]`）                                            | `\d\d` 匹配 "42"          |
|              | `\D`              | 匹配非数字（等价于`[^0-9]`）                                         | `\D+` 匹配 "foo"          |
|              | `\w`              | 匹配单词字符（字母、数字、下划线）                                   | `\w+` 匹配 "user123"      |
|              | `\W`              | 匹配非单词字符                                                       | `\W` 匹配 "@"             |
|              | `\s`              | 匹配空白字符（空格、制表符、换行等）                                 | `\s+` 匹配 "   "          |
|              | `\S`              | 匹配非空白字符                                                       | `\S+` 匹配 "hello"        |
| **量词**     | `*`               | 匹配前一个元素0次或多次                                              | `a*b` 匹配 "b"、"aaab"    |
|              | `+`               | 匹配前一个元素1次或多次                                              | `a+b` 匹配 "ab"、"aaab"   |
|              | `?`               | 匹配前一个元素0次或1次                                               | `colou?r` 匹配 "color"或"colour" |
|              | `{n}`             | 匹配前一个元素恰好n次                                                | `\d{4}` 匹配 "2023"       |
|              | `{n,}`            | 匹配前一个元素至少n次                                                | `a{2,}` 匹配 "aaa"        |
|              | `{n,m}`           | 匹配前一个元素n到m次                                                 | `\d{2,4}` 匹配 "123"      |
| **边界**     | `^`               | 匹配字符串开始位置（多行模式下匹配行首）                             | `^Hello` 匹配行首的"Hello" |
|              | `$`               | 匹配字符串结束位置（多行模式下匹配行尾）                             | `end$` 匹配行尾的"end"     |
|              | `\b`              | 匹配单词边界                                                         | `\bcat\b` 匹配独立的"cat"  |
|              | `\B`              | 匹配非单词边界                                                       | `\Bcat\B` 匹配"category"中的"cat" |
| **分组**     | `(abc)`           | 捕获分组，将多个元素视为一个单元                                     | `(ab)+` 匹配 "abab"       |
|              | `(?:abc)`         | 非捕获分组，不保存匹配结果                                           | `(?:ab)+`                 |
|              | `\|`              | 或操作，匹配左边或右边的表达式                                       | `cat\|dog` 匹配"cat"或"dog" |
| **特殊字符** | `\`               | 转义字符，用于匹配元字符本身                                         | `\.` 匹配 "."             |
| **断言**     | `(?=abc)`         | 正向先行断言，匹配后面跟着"abc"的位置                                | `\d+(?=px)` 匹配"100" in "100px" |
|              | `(?!abc)`         | 负向先行断言，匹配后面不跟"abc"的位置                                | `\d+(?!px)` 匹配"100" in "100pt" |
|              | `(?<=abc)`        | 正向后行断言，匹配前面是"abc"的位置                                  | `(?<=\$)\d+` 匹配"100" in "$100" |
|              | `(?<!abc)`        | 负向后行断言，匹配前面不是"abc"的位置                                | `(?<!\$)\d+` 匹配"100" in "€100" |
| **模式修饰** | `i`               | 忽略大小写模式                                                       | `/hello/i` 匹配"Hello"    |
|              | `g`               | 全局匹配模式                                                         | 通常作为正则标志使用      |
|              | `m`               | 多行模式（使`^`和`$`匹配每行的首尾）                                 | `/^abc/m` 匹配多行中的行首"abc" |
|              | `s`               | 单行模式（使`.`匹配包括换行符在内的所有字符）                        | `/a.b/s` 匹配"a\nb"       |

# ***Mysql支持的所有聚合函数***{#聚合函数大全}

| 聚合函数          | 描述                                                                 | 示例                                                                 |
|-------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `COUNT()`         | 返回匹配行的数量                                                     | `SELECT COUNT(*) FROM users;` 统计用户总数                           |
| `COUNT(DISTINCT)` | 返回唯一值的数量                                                     | `SELECT COUNT(DISTINCT country) FROM users;` 统计不同国家数量        |
| `SUM()`           | 返回数值列的总和                                                     | `SELECT SUM(salary) FROM employees;` 计算工资总和                    |
| `AVG()`           | 返回数值列的平均值                                                   | `SELECT AVG(age) FROM students;` 计算平均年龄                        |
| `MIN()`           | 返回列中的最小值                                                     | `SELECT MIN(price) FROM products;` 查找最低价格                      |
| `MAX()`           | 返回列中的最大值                                                     | `SELECT MAX(temperature) FROM weather;` 查找最高温度                 |
| `GROUP_CONCAT()`  | 将分组中的值连接成字符串（默认逗号分隔）                             | `SELECT GROUP_CONCAT(name) FROM employees;` 合并所有员工姓名         |
| `STD()`           | 返回总体标准差                                                       | `SELECT STD(test_score) FROM exams;` 计算成绩标准差                  |
| `STDDEV()`        | `STD()`的同义词                                                      |                                                                      |
| `VARIANCE()`      | 返回总体方差                                                         | `SELECT VARIANCE(salary) FROM employees;` 计算工资方差               |
| `BIT_AND()`       | 对二进制值执行按位AND操作                                            | `SELECT BIT_AND(flags) FROM permissions;`                            |
| `BIT_OR()`        | 对二进制值执行按位OR操作                                             | `SELECT BIT_OR(flags) FROM permissions;`                             |
| `BIT_XOR()`       | 对二进制值执行按位XOR操作                                            | `SELECT BIT_XOR(status) FROM log;`                                   |
| `JSON_ARRAYAGG()` | 将结果集聚合为JSON数组（MySQL 5.7.22+）                              | `SELECT JSON_ARRAYAGG(name) FROM products;`                          |
| `JSON_OBJECTAGG()`| 将键值对聚合为JSON对象（MySQL 5.7.22+）                              | `SELECT JSON_OBJECTAGG(id, name) FROM products;`                     |