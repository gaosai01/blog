## redis(非关系型数据库)

### 1. 数据库演变

分库降低了单点机器的负载；分表，提高了数据操作的效率，尤其是Write操作的效率。

### 2. redis中数据结构

redis支持的的数据结构有：string，list，hash，set，sort set，bitmap，hyperloglogs

- string： 通过key存储字符串。
- list： 通过key存储双向链表。
- hash： 通过key存储map。
- set： 通过key存储集合。
- sort set： 通过key存储排序了的集合。
- bitmap： 通过key存储一个01串。
- hyperloglogs： i don’t know.

### 3. 常见命令

- select 1: 选择1redis数据库
- set key value：存储一个string 
- info clients： 查看当前的redis连接数
- config get maxclients ： 查询redis允许的最大连接数。
- 。。。

### 3. redis应用

***0. hash或string***

用hash或string存储用户个人信息。

***1. 存储列表*** 

这里尚未考虑数据冗余和性能，并且采用的json序列化。

现在我们有一个论坛系统，论坛中的帖子列表。我们可以存储在sort set中。

zadd zhihu 1000 {fid:1,uid:1,title:2,content:1,time:100000}

然后我们可以从zhihu这个排序集合中查找出一组帖子列表，还支持分页。

用list存储某个帖子所有评论。

***2. bitmap***

我们可以用位运算来实现统计的功能。

统计活跃用户

setbit play:yyyy-mm-dd user_id 1

***3. 自增***

我们可以用redis提供的线程安全的inrc去实现订单号

### 4. redis存储百亿级数据