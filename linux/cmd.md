# 常用命令

### 统计文件行数
```
wc -l a.txt
ll | wc -l
ls -al | wc -l
```

### 统计文件字数
```
wc -w a.txt
cat a.txt | wc -w
```

### 找文件
```
find . -name "a.txt"
```

### 搜索字符
```
ls -al | grep "admin" | grep -v "md"
grep -v :不匹配
grep -r :递归匹配
grep -n :显示行号
```

### 查看端口占用情况
```
lsof -i:8080 # 只能显示当前用户的进程
netstat -ap | grep 9527 # 可以显示其他用户的
```
### 通过进程名查找进程
```
netstat -ap | grep "nginx"
ps -ef | grep "nginx"
```
### 通过进程PID查找监听的端口
```
netstat -ap | grep "39762" | grep "LISTEN"
```
