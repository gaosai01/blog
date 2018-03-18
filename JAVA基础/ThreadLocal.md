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
