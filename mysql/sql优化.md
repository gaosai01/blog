
# sql优化

### 1. 建立合适的索引
建立索引需要遵循最左索引原则。

什么是索引？索引是帮助mysql高效获取数据的数据结构。

过多的索引导致插入时耗时严重，但是查询时迅速。因此建立合适的索引很重要。

建立索引操作形式为:create [unique] index index_user_name on t_user(name)

什么情况下需要建索引?

主键自动建索引,频繁查询字段建索引,查询中与其他表关联的字段建索引,排序字段建索引.
对于频繁更新的字段不适合建索引,where条件始终用不到字段不创建索引,表记录太少不建,经常增删改的表不建,重复率高的字段不建索引（比如性别）

### 2. 慢查询开启-->查看时间长的sql语句，进行优化

开启慢查询命令：set global slow_query_log=on;。 重启后失效

慢查询，多长时间算慢，如何查看：show variables like ‘long_query_time%’;

设置慢查询阙值：set global long_query_time=3;

***explain*** 可查看sql使用的索引和推测执行过程（复杂）

***explain extended + sql*** 可将mysql系统优化的sql加入warning中

***show warning***可查看warning中的sql语句。

### 3. 查看历史sql性能

历史sql语句，可以看到sql执行时间，损耗cpu多少

show profile进行调优。

开启： set global profiling =on;

查看状态：show variables like ‘profiling’;

调用show profile;

修改后查看发现未改变，退出当前会话，重新进入发现改变。

mysql -h localhost -u root -p 123456 -P 3306

结果显示
mysql> show profiles;

+———-+————+———————————+

| Query_ID | Duration | Query |

+———-+————+———————————+

| 1 | 0.00113950 | show variables like ‘profiling’ |

| 2 | 0.25660775 | select from t_user |

查看某条命令详细的执行情况：show profile cpu,block io for query +id;

比如：show profile cpu,block io for query 1;

### 4.实例分享

http://www.cnblogs.com/tangyanbo/p/4462734.html

网上某位大牛的优化记录过程


