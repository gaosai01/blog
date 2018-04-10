# linux配置ssh公钥认证

### 采用的算法

rsa  dsa等

### 生成公钥和私钥

ssh-keygen -t rsa

之后会让输入密钥的名字，

如果输入的话会在当前目录生成文件，

如果不输入的话会在~/.ssh/目录下生成id_rsa.pub和id_rsa两个文件

### 客户端多私钥配置

***问题***：如果出现多台server多个私钥文件，在client主机上怎么配置呢？全部追加到 ~/.ssh/id_rsa 中？经测试，这是行不通的，只有第一个私钥可以用，后面的都不行。

在多台server时，可以有两种解决方案。

1、在不同的主机上，使用相同的公钥，则这些机器的私钥也相同。客户端上只需要配置这一个私钥就可以登录所有的主机。

2、不同的主机上使用不同的公钥时， 这时会有多个不同的私钥。这就需要为不同的主机指定不同的私钥文件，这个配置可以在ssh_config中配置，下面展示一下事例文件配置。

```
# 配置文件地址：~/.ssh/config，这就是文件，不是文件夹
Host * #可不要
IdentityFile /etc/mysecret/gaosai #多行为多个私钥
Port 22 #可不要
User root #可不要
```

### 服务端配置公钥

把公钥复制到server上，这个步骤其实就是刚生成的id_rsa.pub文件的内容追加到ssh server 的../.ssh/authorized_keys

使用命令

```
#cd 进入公钥目录
#cd ~/.ssh/
ssh-copy-id -i id_rsa.pub root@127.0.0.1
```

### 访问即可使用

ssh root@www.cloud.com

### 本地host设置

```
cd /etc
vi hosts

127.0.0.1 www.cloud.com

```





