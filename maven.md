
## 解决maven版本冲突

### 简介

maven冲突的表现形式为找不到类和找不到方法

### 故事

今天开发项目时，对接其他部门的短视频服务，服务端采用apus的rpc框架进行通信。然后apus采用的netty是3.7版本。之前引入的hadoop依赖的netty版本为3.2。这两个netty中某个类的在3.7中新增了构造方法。最后程序执行时报方法找不到。通过搜索，发现了是maven冲突。

然后采用最简单的方法，将我认为需要的jar包版本声明在pom.xml文件的开头。

再次测试，发现问题依然存在。明明是maven冲突，怎么回事呢？

此时就采用查看pom.xml依赖书关系了。

发现3.2的groupId为org.jboss.netty

3.7的groupId为io.netty

可算发现问题所在了，但是org.jboss.netty没有3.7版本。怎么办，当然是忽略啦。采用exclusions忽略3.2版本的依赖，因此就采用了3.7的jar包

个人推测org.jboss.netty可能是io.netty的前身

### 解决办法

### 安装idea的插件 Maven Helper

File/Settings/Plugins/Browse repositories/Maven Helper

### 查看jar包冲突

点击项目中的pom.xml文件，左下角会有dependency analyzar按钮，单击即可查看maven的依赖树

### 移除jar包

在依赖某个库时可以在其中加入exclusions标签

下面的例子表示引入hadoop的jar包，但是不引入hadoop的jar依赖的netty的jar包
```
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.jboss.netty</groupId>
            <artifactId>netty</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```