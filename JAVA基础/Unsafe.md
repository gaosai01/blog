# Unsafe

### 简介

所在包：sun.misc.Unsafe

顾名思义不安全。java中因为不能直接调用内存指针所以安全。此类解决是解决了java不能直接调用指针的问题，也因此违反了java的设计规范，所以命名为不安全。

c语言有三个函数：malloc,relloc,free，分配内存，重新分配内存，释放内存。

### Unsafe单例模式

先看Unsafe的源码

```
  private static Unsafe theUnsafe = new Unsafe();
  
  private Unsafe()
  {
  }
  public static Unsafe getUnsafe(){
	  Class var0 = Reflection.getCallerClass();
     if(!VM.isSystemDomainLoader(var0.getClassLoader())) {
         throw new SecurityException("Unsafe");
     } else {
         return theUnsafe;
     }
  }
```

私有构造函数，无法在类外new，getUnsafe静态公共方法，非BootStrapClassLoader不可调用。因此如果我们要获取unsafe实例，需要利用反射的形式获取theUnsafe，代码如下

```
Field field = Unsafe.class.getDeclaredField("theUnsafe");
field.setAccessible(true);
Unsafe unsafe = (Unsafe)field.get(null);
```

### Unsafe实现技术

jni

### Unsafe可以干什么

1. 直接管理内存
2. 线程挂起与恢复
3. 操作类、对象、变量
4. cas
5. 数组操作
6. 内存屏障
7. 非常规的对象实例化

#### 直接管理内存

##### 开辟内存
```
long address = unsafe.allocateMemory( 4 );
```
上面的语句开辟了四个字节的内存，返回了内存地址address变量

##### 重新分配内存
```
address = unsafe.reallocateMemory( address , 10);
```
上面的语句将内存地址为address的空间大小转成10个字节，返回新地址address

##### 释放内存
```
unsafe.freeMemory(address);
```
上面的语句将内存地址为address的空间释放掉

##### 内存中放元素

```
//将从address的内存开始10个字节都设置成0
unsafe.setMemory(address, 10, (byte)0);
//可依put各种基本类型，address为起点地址
unsafe.putByte(address, (byte) (125));
```

##### 内存中获取元素
```
//获取address内存地址开始的首个字节
unsafe.getByte(address);
```

#### 线程挂起与恢复
```
//相对时间 后面的参数单位是纳秒, 3000000000纳秒后结束挂起
unsafe.park(false, 3000000000l);
//一直挂起
unsafe.park(false, 0);
long time = System.currentTimeMillis()+3000;
//绝对时间后面的参数单位是毫秒。在时间点为time的时候结束挂起
unsafe.park(true, time);
//终止挂起
unsafe.unpark( Thread.currentThread() );
```

#### 操作类、对象、变量
```
class Bean{
	int value;
}
Field v = Bean.class.getDeclaredField("value");
long offset = unsafe.objectFieldOffset(v);
Bean b = new Bean();
b.value = 2;
int value = unsafe.getInt(b, offset);
System.out.println(value);
```

#### 数组管理

```
//获取long[].class第一个元素的首地址（相对首地址）
long offsetArr = unsafe.arrayBaseOffset(long[].class);
```

### cas

### api

```
	//扩充内存  
    public native long reallocateMemory(long address, long bytes);  
      
    //分配内存  
    public native long allocateMemory(long bytes);  
      
    //释放内存  
    public native void freeMemory(long address);  
      
    //在给定的内存块中设置值  
    public native void setMemory(Object o, long offset, long bytes, byte value);  
      
    //从一个内存块拷贝到另一个内存块  
    public native void copyMemory(Object srcBase, long srcOffset, Object destBase, long destOffset, long bytes);  
      
    //获取值，不管java的访问限制，其他有类似的getInt，getDouble，getLong，getChar等等  
    public native Object getObject(Object o, long offset);  
      
    //设置值，不管java的访问限制，其他有类似的putInt,putDouble，putLong，putChar等等  
    public native void putObject(Object o, long offset, Object v);  
      
    //从一个给定的内存地址获取本地指针，如果不是allocateMemory方法的，结果将不确定  
    public native long getAddress(long address);  
      
    //存储一个本地指针到一个给定的内存地址,如果地址不是allocateMemory方法的，结果将不确定  
    public native void putAddress(long address, long x);  
      
    //该方法返回给定field的内存地址偏移量，这个值对于给定的filed是唯一的且是固定不变的  
    public native long staticFieldOffset(Field f);  
      
    //报告一个给定的字段的位置，不管这个字段是private，public还是保护类型，和staticFieldBase结合使用  
    public native long objectFieldOffset(Field f);  
      
    //获取一个给定字段的位置  
    public native Object staticFieldBase(Field f);  
      
    //确保给定class被初始化，这往往需要结合基类的静态域（field）  
    public native void ensureClassInitialized(Class c);  
      
    //可以获取数组第一个元素的偏移地址  
    public native int arrayBaseOffset(Class arrayClass);  
      
    //可以获取数组的转换因子，也就是数组中元素的增量地址。将arrayBaseOffset与arrayIndexScale配合使用， 可以定位数组中每个元素在内存中的位置  
    public native int arrayIndexScale(Class arrayClass);  
      
    //获取本机内存的页数，这个值永远都是2的幂次方  
    public native int pageSize();  
      
    //告诉虚拟机定义了一个没有安全检查的类，默认情况下这个类加载器和保护域来着调用者类  
    public native Class defineClass(String name, byte[] b, int off, int len, ClassLoader loader, ProtectionDomain protectionDomain);  
      
    //定义一个类，但是不让它知道类加载器和系统字典  
    public native Class defineAnonymousClass(Class hostClass, byte[] data, Object[] cpPatches);  
      
    //锁定对象，必须是没有被锁的
    public native void monitorEnter(Object o);  
      
    //解锁对象  
    public native void monitorExit(Object o);  
      
    //试图锁定对象，返回true或false是否锁定成功，如果锁定，必须用monitorExit解锁  
    public native boolean tryMonitorEnter(Object o);  
      
    //引发异常，没有通知  
    public native void throwException(Throwable ee);  
      
    //CAS，如果对象偏移量上的值=期待值，更新为x,返回true.否则false.类似的有compareAndSwapInt,compareAndSwapLong,compareAndSwapBoolean,compareAndSwapChar等等。  
    public final native boolean compareAndSwapObject(Object o, long offset,  Object expected, Object x);  
      
    // 该方法获取对象中offset偏移地址对应的整型field的值,支持volatile load语义。类似的方法有getIntVolatile，getBooleanVolatile等等  
    public native Object getObjectVolatile(Object o, long offset);   
      
    //线程调用该方法，线程将一直阻塞直到超时，或者是中断条件出现。  
    public native void park(boolean isAbsolute, long time);  
      
    //终止挂起的线程，恢复正常.java.util.concurrent包中挂起操作都是在LockSupport类实现的，也正是使用这两个方法
    public native void unpark(Object thread);  
      
    //获取系统在不同时间系统的负载情况  
    public native int getLoadAverage(double[] loadavg, int nelems);  
      
    //创建一个类的实例，不需要调用它的构造函数、初使化代码、各种JVM安全检查以及其它的一些底层的东西。即使构造函数是私有，我们也可以通过这个方法创建它的实例,对于单例模式，简直是噩梦，哈哈  
    public native Object allocateInstance(Class cls) throws InstantiationException;
```
