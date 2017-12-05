## java基础知识之线程

> 2017 ／ 8 ／ 9

#### 1. 线程有两种实现方法。
一种是Thread，另一种是Runable

采用Runable可以数据共享，和扩展，这就是Runable的好处。因为Runable是接口。

多个线程，同一个Runable可以实现共享数据。

#### 2. 线程的方法，join，interrpt，yeild

join是等待线程结束的方法

yield是让出当前线程，大家公平竞争，可能依然没人撼动地盘，所以当前线程可能仍继续执行。也可能之后执行。

interrupt是中断等待，调用次方法，若线程存在等待其他线程响应的表达式，则会抛出中断的异常。

sleep让当前线程沉睡

setPriority设置当前线程的优先级

setDaemon设置当前线程为守护线程，必须在start之前设置

#### 3. CountDownLatch 计数锁
这个类的构造函数为countDown(int);代表需要countDown的次数

这个类的对象有countDown() 和 await() 方法

其中countDown调用一次次数减一。当为0时才会执行await函数后面的代码。

其中await方法是等待次数变成0。

#### 4. java信号量(Semaphore)

操作系统的信号量主要用于进程控制，可以控制某个资源被同时访问的个数等。java并发库的Semaphore可以轻松完成信号量的控制。

构造方法确定同时访问进程的个数，在代码中通过`acquire`获取许可，`release`释放许可，示例代码如下：

```
Semaphore semaphore = new Semaphore(5);
for ( int i = 0; i < 20; i++) {
    final int t = i;
    new Thread(()->{
        try {
            semaphore.acquire();   //获取许可
            System.out.println(""+t+"在执行");
            Thread.sleep(1000);
            semaphore.release();   //释放许可
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }).start();
}
```




#### 5. 线程池的知识

`Executors`  `Executor` 的两个类，一个工厂类，一个产品类。

通过Executors可以创建以下几种线程池

* ***newCachedThreadPool***:可缓冲线程池，灵活回收线程，如果没有可回收的线程，则创建新的线程
* ***newFixedThreadPool***:定长线程池，超出的线程会等待
* ***newScheduledThreadPool***:定时或周期执行的线程池
* ***newSingleThreadExecutor***:单线程化线程池，只有一个线程来工作，保证所有工作按序执行。
* ***newSingleThreadScheduledExcutor***：single和scheduled合并的线程池，创建单一线程并定时或周期执行
* ***newWorkStealingPool***：创建持有足够线程的线程池来支持给定的并行级别，并通过使用多个队列，减少竞争，它需要穿一个并行级别的参数，并行级别决定了同一时刻最多有多少个线程在执，如果不传，则被设定为默认的CPU数量.

* shutDown() 

    当线程池调用该方法时,线程池的状态则立刻变成SHUTDOWN状态。此时，则不能再往线程池中添加任何任务，否则将会抛出RejectedExecutionException异常。但是，此时线程池不会立刻退出，直到添加到线程池中的任务都已经处理完成，才会退出。
 
* shutdownNow() 

     根据JDK文档描述，大致意思是：执行该方法，线程池的状态立刻变成STOP状态，并试图停止所有正在执行的线程，不再处理还在池队列中等待的任务，当然，它会返回那些未执行的任务。 
     它试图终止线程的方法是通过调用Thread.interrupt()方法来实现的，但是大家知道，这种方法的作用有限，如果线程中没有sleep 、wait、Condition、定时锁等应用, interrupt()方法是无法中断当前的线程的。所以，ShutdownNow()并不代表线程池就一定立即就能退出，它可能必须要等待所有正在执行的任务都执行完成了才能退出。  


### 6. Future 和 Callback 和 FutureTask


> 新增时间：2017/08/16

### 7. 锁synchronized和ReentrantLock和ReentantReadWriteLock

***synchronized 两种用法***

1. 变量锁

	```java
	private static volatile A instance = null;
	
	public static A instance(){
		if( instance == null ){
			synchronized (instance) {
				if( instance == null ){
					instance = new A();
				}
			}
		}
		return instance;
	}

	```
	获取instance资源的锁权限 是 ***当前线程***获取instance资源的锁。一个线程多次获区某一资源的锁，并且在线程结束时才释放，不会发生死锁或者等待，可以解释为已有资源的锁，无需等待。
2. 方法锁
	
	***例子1***:方法a与a多线程互斥，b与b互斥，a与b互斥
	
	因为他们实质上是对this加的锁。所以a与b都需要获取this的锁，那么就会发生等待。
	
	```java
	public synchronized void a(){
		System.out.println( "1" );
	}
	public synchronized void b(){
		System.out.println( "2" );
	}
	```
	***例子2***:锁是针对于线程的，如果有锁，再次获取无须等待。
	
	某一线程执行a方法不回发生死锁。
	
	```java
	public synchronized void a(){
		System.out.println( "1" );
		b();
	}
	public synchronized void b(){
		System.out.println( "2" );
	}
	```
	
***Lock的用法***

```java
Lock lock = new ReentrantLock();
lock.lock();
lock.unlock();
ReentrantReadWriteLock rwlock = new ReentrantReadWriteLock();
rwlock.writeLock().lock();
new Thread(){
	public void run(){
		rwlock.readLock().lock();
	}
}.start();
//这串代码会发生死锁
```	

读写锁分为读锁和写锁，***规则***如下：

1）如果某线程申请了读锁，其它线程可以再申请读锁，但不能申请写锁。

2）如果某线程申请了写锁，其它线程不能申请读锁，也不能申请写锁。

### ReentrantLock && ReadWriteLock && StampedLock

### 8. 自旋锁 和 阻塞锁

阻塞锁的优势在于，阻塞的线程不会占用cpu时间， 不会导致 CPu占用率过高，但进入时间以及恢复时间都要比自旋锁略慢。

在竞争激烈的情况下 阻塞锁的性能要明显高于 自旋锁。

理想的情况则是; 在线程竞争不激烈的情况下，使用自旋锁，竞争激烈的情况下使用，阻塞锁。

### Fork/Join模型
ForkJoinPool 
ForkJoinTask的子类
RecursiveTask代表有返回值的任务
RecursiveAction代表没有返回值的任务


	
