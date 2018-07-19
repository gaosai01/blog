## java基础知识之线程

#### 线程的状态

1、***新建状态(New)***：新创建了一个线程对象。

2、***就绪状态(Runnable)***：线程对象创建后，其他线程调用了该对象的start()方法。该状态的线程位于“可运行线程池”中，变得可运行，只等待获取CPU的使用权。即在就绪状态的进程除CPU之外，其它的运行所需资源都已全部获得。

3、***运行状态(Running)***：就绪状态的线程获取了CPU，执行程序代码。

4、***阻塞状态(Blocked)***：阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。

阻塞的情况分三种：

(1)、等待阻塞：运行的线程执行wait()方法，该线程会释放占用的所有资源，JVM会把该线程放入“等待池”中。进入这个状态后，是不能自动唤醒的，必须依靠其他线程调用notify()或notifyAll()方法才能被唤醒，

(2)、同步阻塞：运行的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入“锁池”中。

(3)、其他阻塞：运行的线程执行sleep()或join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。

5、***死亡状态(Dead)***：线程执行完了或者因异常退出了run()方法，该线程结束生命周期。

#### 线程与进程的区别

（1）、进程是资源的分配和调度的一个独立单元，而线程是CPU调度的基本单元

（2）、同一个进程中可以包括多个线程，并且线程共享整个进程的资源（寄存器、堆栈、上下文），一个进行至少包括一个线程。

（3）、进程的创建调用fork或者vfork，而线程的创建调用pthread_create，进程结束后它拥有的所有线程都将销毁，而线程的结束不会影响同个进程中的其他线程的结束

（4）、线程是轻两级的进程，它的创建和销毁所需要的时间比进程小很多，所有操作系统中的执行功能都是创建线程去完成的

（5）、线程中执行时一般都要进行同步和互斥，因为他们共享同一进程的所有资源

（6）、线程有自己的私有属性TCB，线程id，寄存器、硬件上下文，而进程也有自己的私有属性进程控制块PCB，这些私有属性是不被共享的，用来标示一个进程或一个线程的标志

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

#### CyclicBarrier

可循环使用（Cyclic）的屏障（Barrier）

内置方法

await：调用同步屏障对象的此方法，当前线程阻塞，并且num--，直到对象的之变为0.开始执行线程下面的代码块。（此时阻塞的线程有cpu调度开始执行）

reset：重置num

getNumberWaiting： 可以获得CyclicBarrier阻塞的线程数量

isBroken： 用来知道阻塞的线程是否被中断

#### Exchanger

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
     它试图终止线程的方法是通过调用Thread.interrupt()方法来实现的，但是大家知道，这种方法的作用有限，如果线程中没有***sleep 、wait、Condition、定时锁等应用***, interrupt()方法是无法中断当前的线程的。所以，ShutdownNow()并不代表线程池就一定立即就能退出，它可能必须要等待所有正在执行的任务都执行完成了才能退出。  
     
### 线程池的拒绝策略

Excutors的静态方法默认创建的对象为：ThreadPoolExecutor实例

```
public ThreadPoolExecutor(int corePoolSize,
		 int maximumPoolSize,
 		 long keepAliveTime, TimeUnit unit,
  		 BlockingQueue workQueue, 
  		 ThreadFactory threadFactory,
   		 RejectedExecutionHandler handler); 
```

参数名、说明： 

1. corePoolSize 线程池维护线程的最少数量 
2. maximumPoolSize 线程池维护线程的最大数量 
3. keepAliveTime 线程池维护线程所允许的空闲时间 
4. workQueue 任务队列，用来存放我们所定义的任务处理线程 
5. threadFactory 线程创建工厂 
6. handler 线程池对拒绝任务的处理策略 

解释拒绝处理策略

四种默认实现

AbortPolicy策略 抛出error

CallerRunsPolicy策略 当前调用线程执行runable

DiscardOledestPolicy策略 将最早将入队列的移除（忽律最早的），然后加入对列

DiscardPolicy策略 忽律当前runable

自定义拒绝策略处理气

```
   public class SystemLogPolicy implements RejectedExecutionHandler {
        
       public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            System.out.println( "Task " + r.toString() +
                                " rejected from " +
                                e.toString() );
        }
    }
```


### 6. Future 和 Callback 和 FutureTask 和 Runable



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

```
public class CountTask extends RecursiveTask<Integer>{

    private static final int THREAD_HOLD = 2;

    private int start;
    private int end;

    public CountTask(int start,int end){
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        int sum = 0;
        //如果任务足够小就计算
        boolean canCompute = (end - start) <= THREAD_HOLD;
        if(canCompute){
            for(int i=start;i<=end;i++){
                sum += i;
            }
        }else{
            int middle = (start + end) / 2;
            CountTask left = new CountTask(start,middle);
            CountTask right = new CountTask(middle+1,end);
            //执行子任务
            left.fork();
            right.fork();
            //获取子任务结果
            int lResult = left.join();
            int rResult = right.join();
            sum = lResult + rResult;
        }
        return sum;
    }

    public static void main(String[] args){
        ForkJoinPool pool = new ForkJoinPool();
        CountTask task = new CountTask(1,4);
        Future<Integer> result = pool.submit(task);
        try {
            System.out.println(result.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

### ThreadFactory线程工厂

主要用于newThread,对Thread进行初始化

```
public interface ThreadFactory {
    Thread newThread(Runnable r);
}


	static class DefaultThreadFactory implements ThreadFactory {
        private static final AtomicInteger poolNumber = new AtomicInteger(1);
        private final ThreadGroup group;
        private final AtomicInteger threadNumber = new AtomicInteger(1);
        private final String namePrefix;

        DefaultThreadFactory() {
            SecurityManager s = System.getSecurityManager();
            group = (s != null) ? s.getThreadGroup() :
                                  Thread.currentThread().getThreadGroup();
            namePrefix = "pool-" +
                          poolNumber.getAndIncrement() +
                         "-thread-";
        }

        public Thread newThread(Runnable r) {
            Thread t = new Thread(group, r,
                                  namePrefix + threadNumber.getAndIncrement(),
                                  0);
            if (t.isDaemon())
                t.setDaemon(false);
            if (t.getPriority() != Thread.NORM_PRIORITY)
                t.setPriority(Thread.NORM_PRIORITY);
            return t;
        }
    }
```

### Thread的属性

***name***：线程名称，可以重复，若没有指定会自动生成。

***id***：线程ID，一个正long值，创建线程时指定，终生不变，线程终结时ID可以复用。

***priority***：线程优先级，取值为1到10，线程优先级越高，执行的可能越大，若运行环境不支持优先级分10级，如只支持5级，那么设置5和设置6有可能是一样的。

***state***：线程状态，Thread.State枚举类型，有NEW、RUNNABLE、BLOCKED、WAITING、TIMED_WAITING、TERMINATED 5种。

***ThreadGroup***：所属线程组，一个线程必然有所属线程组。

***UncaughtExceptionHandler***：未捕获异常时的处理器，默认没有，线程出现错误后会立即终止当前线程运行，并打印错误

***threadLocals***: Thread里面有一个threadLocals的Map。他是ThreadLocal的实现方案。

### ThreadGroup类

***属性***

name：当前线程的名称。

parent：当前线程组的父线程组。

MaxPriority：当前线程组的最高优先级，其中的线程优先级不能高于此

***方法***

public int activeCount(); // 获得当前线程组中线程数目， 包括可运行和不可运行的 

public int activeGroupCount()； //获得当前线程组中活动的子线程组的数目 
       
public int enumerate（Thread list[]）; //列举当前线程组中的线程 

public int enumerate（ThreadGroup list[]）； //列举当前线程组中的子线程组 

public final int getMaxPriority（）; //获得当前线程组中最大优先级 

public final String getName（）； //获得当前线程组的名字 

public final ThreadGroup getParent（）; //获得当前线程组的父线程组 

public boolean parentOf（ThreadGroup g）； //判断当前线程组是否为指定线程的父线程 

public boolean isDaemon（）; //判断当前线程组中是否有监护线程 

public void list（）; //列出当前线程组中所有线程和子线程名 



	
#### 计算集群数目

概念：qps代表每s种相应的用户请求数量

有一个公式为： qps = 并发量 ／ 每个的平均响应时间

每台机器的的qps为10。那么一天的请求量为100w，并且请求均匀分布，请问需要服务端多少台机器?

答： 每台机器一天响应多少个请求呢？  10 * 60 * 60 * 24 = 864000。

那么100w的请求量需要  100 w ／ 864000 = 1.16 台。 

那么需要服务器2台。可以让另一台性能差一点，然后负载均衡时权重小一些。