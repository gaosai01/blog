# sql隐式类型转化

---

### 简介
在程序开发中，经常会和数据库打交道，也因此我们会更加看重sql语句的执行效率，如果需要学习索引的使用可以看这篇文章[mysql创建索引经验](https://github.com/crossoverJie/JCSprout/blob/master/MD/SQL-optimization.md)，本文主要讲解sql语句中暗藏的隐式类型转化的知识,首先我门先看几个案例。

### 案例一:varchar和long的转化
#### t1的表结构
|name| type |
|----|------|
| id |  int |
| tel|varchar|
#### t1表的数据

|id|tel|
|---|---|
|1|130++++++0|
|2|+++++++++0|
|n|17+++++++0|

#### sql对比
```
-- 慢
select * from t1 where tel=13071587169;
-- 快
select * from t1 where tel='13071587169';
```

本人的测试结果

```
mysql> select count(*) from t1;
+----------+
| count(*) |
+----------+
|  1000000 |
+----------+
1 row in set (0.21 sec)

mysql> select tel from t1 where tel=100000;
+--------+
| tel    |
+--------+
| 100000 |
+--------+
1 row in set (0.27 sec)

mysql> select tel from t1 where tel="100000";
+--------+
| tel    |
+--------+
| 100000 |
+--------+
1 row in set (0.08 sec)
```

因为tel全局被强制转化成了float

所以请注意表中如果存储了时间戳和手机号这种请保持类型一致

#### 案例二:varchar转float失败结果为0
看了这个标题您可能一脸蒙蔽，听我一一分析。

当表中某一列为varchar类型时，用它和数字0判断是否相等，此时需要发生隐式类型转化，varchar转float，在这个varchar无法正常转成float，比如a12这个转float时会抛出异常，这时候varchar的值会变成默认值0。此处语言描述都是苍白的，请看详细的代码描述。

```
mysql> desc t2;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| str   | varchar(11) | NO   |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
2 rows in set (0.09 sec)

mysql> select * from t2;
+----+-----+
| id | str |
+----+-----+
|  1 | a10 |
|  2 | b11 |
|  3 | d44 |
|  4 | c66 |
+----+-----+
4 rows in set (0.08 sec)

mysql> select * from t2 where str = 0;
+----+-----+
| id | str |
+----+-----+
|  1 | a10 |
|  2 | b11 |
|  3 | d44 |
|  4 | c66 |
+----+-----+
4 rows in set, 4 warnings (0.08 sec)
```

对上述代码描述，t2表中有4条记录，其中str字段为varchar，4行记录varchar转float都会抛出异常，因此类型转化结果为0，执行sql语句会显示4条记录。

接下来说一下这种的应用场景
```
select * from t_user where tel="130****7189" and pwd = 0;
```
大家根据上面的描述这条sql语句会执行成功，并拿出用户，其原理是用了隐式类型转化的知识(如果万一代码写错了，这将导致严重问题)

#### null的判断

```
mysql> desc t2;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| str   | varchar(11) | NO   |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
2 rows in set (0.09 sec)

mysql> select * from t2;
+----+-----+
| id | str |
+----+-----+
|  1 | a10 |
|  2 | b11 |
|  3 | d44 |
|  4 | c66 |
+----+-----+
4 rows in set (0.08 sec)

mysql> select * from t2 where null  = null;
Empty set (0.07 sec)

mysql> select * from t2 where null  <=> null;
+----+-----+
| id | str |
+----+-----+
|  1 | a10 |
|  2 | b11 |
|  3 | d44 |
|  4 | c66 |
+----+-----+
4 rows in set (0.05 sec)
```

两个参数至少有一个是 NULL 时，比较的结果也是 NULL，例外是使用 <=> 对两个 NULL 做比较时会返回 1，这两种情况都不需要做类型转换

### 隐式类型转换有哪些

1. 两个参数至少有一个是 NULL 时，比较的结果也是 NULL，例外是使用 <=> 对两个 NULL 做比较时会返回 1，这两种情况都不需要做类型转换

2. 两个参数都是字符串，会按照字符串来比较，不做类型转换

3. 两个参数都是整数，按照整数来比较，不做类型转换

4. 十六进制的值和非数字做比较时，会被当做二进制串

5. 有一个参数是 TIMESTAMP 或 DATETIME，并且另外一个参数是常量，常量会被转换为 timestamp

6. 有一个参数是 decimal 类型，如果另外一个参数是 decimal 或者整数，会将整数转换为 decimal 后进行比较，如果另外一个参数是浮点数，则会把 decimal 转换为浮点数进行比较

7. 所有其他情况下，两个参数都会被转换为浮点数再进行比较(这点包含了字符串与整数0的比较转换是varchar转float)

### 隐式类型转化的例子

```
mysql> select 1+'1';
+-------+
| 1+'1' |
+-------+
|     2 |
+-------+
1 row in set (0.07 sec)

mysql> select 1+'1a';
+--------+
| 1+'1a' |
+--------+
|      2 |
+--------+
1 row in set, 1 warning (0.08 sec)

mysql> select 1+'a1a';
+---------+
| 1+'a1a' |
+---------+
|       1 |
+---------+
1 row in set, 1 warning (0.08 sec)

mysql> select 1+'11aaaa';
+------------+
| 1+'11aaaa' |
+------------+
|         12 |
+------------+
1 row in set, 1 warning (0.07 sec)
```

### 友情提示

sql的隐式类型转化不仅会导致sql语句的性能问题，严重可能导致数据丢失。




