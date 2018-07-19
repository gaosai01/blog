# java调优-jstack

### jstack 用途

jstack常用做 ***定位死循环、线程阻塞、死锁等问题***

### jstack使用

jstack pid

这条命令会列举出java的pid进程中的各个线程信息，包括线程状态，阻塞时会显示哪里

### 线程状态
线程一般存在如下几种状态：

1、RUNNABLE，线程处于执行中

2、BLOCKED，线程被阻塞

3、WAITING，线程正在等待

### 实例：jstack找出最耗cpu的线程并定位代码

利用到的命令 

- jps(ps -ef|grep java) 查看java的进程id
- top 查看某个进程里面的线程信息
- printf 利用格式话输出，将10进程转化成16进制 printf "%x\n" 21742
- jstack 查看某个java进程的各个线程信息
- grep 搜索输出

我们先运行如下的java应用

```
public class App {
	public static void main(String[] args) {

		while (true) {
			
		}
	}
}
```

然后找到这个java应用的pid,调用jps命令得到此App的pid为2463

然后我们通过 top -Hp 2463 查看2463进程下的线程信息,找到最耗时的线程

然后通过 jstack 2463 查看此进程下面的线程信息

可以看到如下信息

```
"main" #1 prio=5 os_prio=31 tid=0x00007f8dae003800 nid=0x1c03 runnable [0x0000700003161000]
   java.lang.Thread.State: RUNNABLE
	at com.gaosai.test.App.main(App.java:10)
```

可以看到main在App的第10行运行，定位代码发现此处有死循环