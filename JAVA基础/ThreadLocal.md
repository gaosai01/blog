# java基础-ThreadLocal解析

#### 每一个Thread有一个属性，threadLocals，他的类型为Map。

#### ThreadLocal的get方法

```
	  public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map =t. threadLocals;
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }
```

解释：

1. 通过Thread.currentThread()拿到当前线程
2. 拿到Thread的threadLocals对象
3. 通过threadLocals对象拿到key的value


#### 出现内存泄漏

上文出现了一个threadLocals的属性（Thread的），这是一个弱引用key的伪Map（不实现Map接口）

因此如果不存在ThreadLocal对象的强引用的话，那么map中的key值为null了，而value值不为null，此时出现了内存泄漏，如果thread一直运行，内存泄漏就会一直存在。

解决方案：ThreadLocal变成静态的，他才能强引用一直存在。