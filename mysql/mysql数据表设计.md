## 数据库设计

### 试题1

业务需求：用户表(uid,name)，关注表( from_uid, to_uid ), 用户动态表( uid, message, time )。

问题1：求出uid为1的用户关注的人的动态按时间排序前10条

```
select * from 用户动态表 where uid in
 ( select to_uid from 关注表 where from_uid = 1 ) 
 order by time desc limit 0, 10
```

```
select 用户动态表.* from 用户动态表 left join 关注表 on 用户动态表.uid = 关注表.to_uid
where 关注表.from_uid = 1 order by time limit 0, 10
```

问题2：如果uid为1的用户关注了100w个人，那么上面的sql性能肯定差，如何优化，空间换时间
