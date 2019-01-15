# jvm-params

### 内存参数

#### -Xms2048m
设置初始堆大小为2G，会根据空余堆内存比例(MaxHeapFreeRatio)动态调整实时堆的大小
#### -Xmx2048m
设置最大堆内存为2G，会根据空余堆内存比例动态调整实时堆的大小。堆大小的范围是[-Xms,-Xmx]
#### -Xmn512m
设置年轻代的大小为512M，增大年轻代会减少年老代的大小，从而增加FullGC的概率，再而影响系统性能。Sun官方推荐配置成整个堆的3/8
#### -XX:NewSize=256m
设置初始化年轻代大小，会递增到-XX:MaxNewSize配置。优先级低于-Xmn,高于-XX:NewRatio
#### -XX:MaxNewSize=512m
设置年轻代的最大大小，优先级低于-Xmn,高于-XX:NewRatio
#### -XX:NewRatio=4
设置老年代与年轻代的比例，事例给出年轻代占整个堆的1/5。优先级低于-Xmn
#### -Xss1024K
设置单个线程的栈大小,JDK5以后每个线程堆栈大小为1M,以前每个线程堆栈大小为256K. 在相同物理内存下,减小这个值可以生成更多的线程.但是操作系统对一个进程内的线程数还是有限制的,不能无限生成，受ulimit等多方面限制。对于一般的应用，如果栈不是很深，设置成128k够用，对于大的应用建议使用256k。这个选项对性能影响比较大，需要严格的压测才能线上使用
#### -XX:ThreadStackSize=1024K
等价于-Xss配置,设置单个线程的栈大小为1M
#### -XX:SurvivorRatio=8
年轻代中Eden区与单个Survivor区的比例，事例中单个Survivor占年轻代的1/10。
#### -XX:MaxDirectMemorySize=1024m
直接内存的大小，上述配置为1G。堆外内存的大小，不算在Xmx内存中
#### -XX:MetaspaceSize=64m
元空间大小，jdk8新增配置，用于替换PermSize
#### -XX:MaxMetaspaceSize=64m
最大的元空间大小
#### -XX:PermSize=64m
持久代的初始大小，持久代=(perm gen)，1.8以前的概念
#### -XX:MaxPermSize=64m
持久代的最大值

### GC日志参数
#### -XX:+PrintGC
简单GC日志模式
#### -XX:+PrintGCDetails
开关类型，打印gc日志详细信息，默认关闭
#### -XX:+PrintGCTimeStamps
开关类型，打印gc发生时相对jvm启动的时间戳
#### -XX:+PrintGCDateStamps
开关类型，打印gc发生的日期
#### -Xloggc:/home/gaosai/logs/gc.log
将gc记录保存到制定位置
#### -XX:+UseGCLogFileRotation
开关类型，gc日志文件滚动存储，默认不滚动，需要配置Xloggc和
#### -XX:NumberOfGCLogFiles=20
gc日志文件的循环数量，事例表明最多20个gc日志文件
#### -XX:GCLogFileSize=10M
单个gc日志文件的大小,事例中为10M。最小值8K


### GC算法参数
#### -XX:MaxTenuringThreshold=7
对象进入到老年代的年龄，默认15。如果设置为0的话,对象在年轻代不经过Survivor区,直接进入年老代. 对于年老代比较多的应用,可以提高效率.如果将此值设置为一个较大值,则年轻代对象会在Survivor区进行多次复制,这样可以增加对象再年轻代的存活时间,增加在年轻代即被回收的概率，该参数只有在串行GC时才有效.
#### -XX:PretenureSizeThreshold=1024
对象超过多大是直接在旧生代分配，单位字节，新生代采用Parallel Scavenge GC时配置无效，另一种直接在旧生代分配的情况是大的数组对象,且数组中无外部引用对象.
#### -XX:+DisableExplicitGC
开关类型，忽略System.gc()
#### -Xnoclassgc
开关类型，禁用类的垃圾回收
#### -XX:+CollectGen0First
FullGC时是否先YGC
#### -XX:SoftRefLRUPolicyMSPerMB=1
每兆堆空闲空间中SoftReference（软引用）的存活时间，事例表明每兆堆中的空闲空间中的softreference会（在最后一个强引用被回收之后）存活1秒钟|

### 其他调优参数
#### -XX:+UseFastAccessorMethods
原始类型的快速优化
#### -XX:+AggressiveOpts
加快编译
#### -XX:+UseBiasedLocking
锁机制的性能改善,使用偏向锁，减少无多线程竞争下的性能消耗
#### -XX:TLABWasteTargetPercent
TLAB占eden区的百分比
#### -XX:MinHeapFreeRatio
扩容堆的比例,指定 jvm heap 在使用率小于 n 的情况下 ,heap 进行收缩 ,Xmx==Xms 的情况下无效 , 如 :-XX:MinHeapFreeRatio=30
#### -XX:MaxHeapFreeRatio
扩容堆的比例,指定 jvm heap 在使用率大于 n 的情况下 ,heap 进行扩张 ,Xmx==Xms 的情况下无效 , 如 :-XX:MaxHeapFreeRatio=70

### 提示
-XX开头的配置是不稳定的选项，是非标准的选项
