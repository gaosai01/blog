## sql语句中的join表达式

### 内连接，左连接，右连接，外连接

### 对于左连接和右连接本人一直记忆不清，到底哪个表数据会null

今天恍然大悟，记录一下

**A left/right join B**

物理位置 记录 A左，B右。join前的方向英文标示着物理位置为此英文的反方向为null，另一种记忆方式为join前的方向词表示此方向的表需要是完全集

**A left join B**     B表会出现null，A表的完全集

**A right join B**    A表会出现null，B表的完全集