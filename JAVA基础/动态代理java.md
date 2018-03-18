
### 动态代理可以干什么

可以实现aop机制

### 动态代理怎么做到的。

通过classloader加载时的字节码修改实现类，实现了中可以有before和after方法。

### 动态代理有哪几种方法

jdk自带的Proxy InvcationHander

cglib 速度比jdk自带的快，无需接口，直接生成所需要类的子类

javassist 一个开源项目