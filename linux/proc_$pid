# /proc/$pid

### /proc/$pid/cwd
这是一个外链，用于指向命令执行的目录

```
lrwxrwxrwx 1 root root 0 12月 24 19:35 cwd -> /home/guest/nginx
```
### /proc/$pid/exe
这是一个外链，对应着执行的命令文件

```
lrwxrwxrwx 1 root root 0 12月 24 19:35 exe -> /home/guest/nginx/sbin/nginx
```
### /proc/$pid/cmdline
这是个文件,保存着启动命令

```
[root@guest 39925]# cat cmdline 
nginx: master process ./sbin/nginx
```
### /proc/$pid/environ
这是个文件，保存着命令启动时的环境变量

```
HOSTNAME=gs
```

### /roc/$pid/fd
这是个文件夹，里面保存着进程打开的文件句柄

```
[root@guest 39762]# cd fd
[root@guest fd]# ll
总用量 0
l-wx------ 1 root root 64 12月 21 18:32 0 -> /dev/null
l-wx------ 1 root root 64 12月 21 18:32 1 -> /home/guest/dubbo-admin/output/nohup.out
l-wx------ 1 root root 64 12月 21 18:32 11 -> /home/guest/dubbo-admin/log/ACCESS.log
l-wx------ 1 root root 64 12月 21 18:32 12 -> /home/guest/dubbo-admin/log/ERROR.log
lrwx------ 1 root root 64 12月 21 18:32 13 -> socket:[3894482832]
lrwx------ 1 root root 64 12月 21 18:32 14 -> socket:[3894482833]
l-wx------ 1 root root 64 12月 21 18:32 17 -> /home/guest/dubbo-admin/log/INFO.log
l-wx------ 1 root root 64 12月 21 18:32 18 -> /home/guest/dubbo-admin/log/DEBUG.log
```
