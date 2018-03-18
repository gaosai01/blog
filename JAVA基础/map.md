## java基础知识 之 map

此文主要讲解HashMap，HashTable，ConcurrentHashMap，TreeMap，LinkedHashMap

高度注意 Map 类集合 K/V 能不能存储 null 值的情况
Hashtable，key：不允许为 null，value：不允许为 null，备注：线程安全
ConcurrentHashMap，key：不允许为 null，value：不允许为 null，备注：分段锁技术
TreeMap，key：不允许为 null，value：允许为 null，备注：线程不安全
HashMap，key：允许为 null，value允许为 null，备注：线程不安全

#### 1. TreeMap
这是一个自动排序的平衡二叉树，基于红黑树算法

TreeMap的使用方法

```java
//指定排序器  
TreeMap<String, String> treeMap = new TreeMap<String, String>(new 
	Comparator<String>(){  
            /* 
             * int compare(Object o1, Object o2) 返回一个基本类型的整型， 
             * 返回负数表示：o1在前， 
             * 返回0 表示：o1和o2顺序不变， 
             * 返回正数表示：o2在前。 
             */  
            public int compare(String o1, String o2) {
                //指定排序器按照降序排列  
                return o2.compareTo(o1);  
            }     
   });

```

插入时间复杂度o(logn)，查找时间复杂度o(logn)，创建时间复杂度o(nlogn)

**TreeMap主要用于建造按key排序的键值对**。

### 2. HashMap和HashTable

这两个数据结构经常用于各种面试题，因此需要掌握他们的异同之处。

##### 共同点： HashMap和HashTable实现的方法都是： 数组 + 链表。

##### 共同点： 时间复杂度一样（因为存储采用的方法是一样的）。

我们先关注他前面那个Hash单词，我们都知道哈希算法，通过哈希算法将key值转化成index角标，每次插入时写入数组的index角标对应的链表，因此插入的时间复杂度为o(1)，那么查找的话先通过Hash算法将key转化成index角标，然后读取当前index下的链表，查找对应的key和value，然而一般情况下链表长度也为1，因此查找的时间复杂度为o(1)。那么可以得出创建的时间复杂度为o(n)。

因为时间复杂度是o(1)，那么面试时经常有各种算法方面的题目，可以考虑这种存储方案。可以将算法的时间复杂度从o(n2)降到o(nlogn)再降到o(n)。接下来我会在文章结尾列举两道算法题目，***1.寻找两个集合的交集和并集 2.给定一个整数数组找出其中两个数相加等于目标值。***

不同之处便是在线程安全和效率和存储的数据方面了

##### 不同点： HashMap可以接受为null的键值(key)和值(value)，而HashTable则不行。

##### 不同点： HashTable是线程安全的，而HashMap是线程不安全的

HashMap在多线程下因为没有加同步锁，可能会导致插入失败，修改失败，删除失败。（resize情况很好解释）

解释一下为什么会发生插入失败，修改失败，删除失败。

当某些情况下，不同的key值计算出来的index角标相同。那么此时一旦有多个线程里面的key计算出来的index角标相同，那么这时候就会修改同一个链表。

在对应的数组位置存放链表的头结点。对链表而言，**新加入的节点会从头结点加入**。这句话是多线程不安全的关键。


### 3. ConCurrentHashMap

这是一个java5新加的类，它是HashTable的替代，比HashTable的扩展性更好。
因此他的速度和线程安全都是最好的，在多线程下共享数据用它最合适不过了。

### 4. LinkedHashMap
LinkedHashMap和HashMap基本上一样，他依然是线程不安全的

前面多了个Linked单词，它保存了建值对的插入顺序，因此用迭代器遍历Map时，遍历的顺序和插入顺序一样。

> 作者：高赛
> 
> 时间：2017 / 8 / 8

***

> 两道算法题目
> 
> 2017 ／ 8 ／ 9

### 1. 两个集合的交集

```java
	//三种算法，n2，nlogn，n
	public List<Integer> jiaoji_n2( List<Integer> a, List<Integer> b ){
		List<Integer> ans = new LinkedList<>();
		for( Integer i : a ){
			for( Integer j : b ){
				if( i.equals( j ) ){
					ans.add( i );
				}
			}
		}
		return ans;
	}
	
	public List<Integer> jiaoji_nlogn( List<Integer> a, List<Integer> b ){
		List<Integer> ans = new LinkedList<>();
		if( a.size() > b.size() ){
			List<Integer> tmp = a;
			a = b;
			b = tmp;
		}
		Set<Integer> set = new TreeSet<>();
		for( Integer i : b ){
			set.add( i );
		}
		for( Integer i : a ){
			if( set.contains( i ) ){
				ans.add( i );
			}
		}
		return ans;
	}
	
	public List<Integer> jiaoji_n( List<Integer> a, List<Integer> b ){
		List<Integer> ans = new LinkedList<>();
		if( a.size() > b.size() ){
			List<Integer> tmp = a;
			a = b;
			b = tmp;
		}
		Set<Integer> set = new HashSet<>();
		for( Integer i : b ){
			set.add( i );
		}
		for( Integer i : a ){
			if( set.contains( i ) ){
				ans.add( i );
			}
		}
		return ans;
	}
```

### 2. 不同的两个数和为n

```java
	//此处只写出时间复杂度为n的算法
	public void sum( List<Integer> a ){
		int sum = 10;
		int size = a.size();
		Map<Integer, Integer> map = new HashMap<>();
		for( int i = 0; i<size; ++i ){
			map.put( a.get( i ) , i);
		}
		//未去重
		for( int i = 0; i<size; ++i ){
			Integer j = map.get( sum - a.get( i ) );
			if( j != null && i != j ){
				System.out.println( String.format
						("%d和%d为%d", a.get( i ), sum-a.get( i ), sum ) );
			}
		}
	}
	
```

### HashTable的put方法

```
	public synchronized V put(K key, V value) {
        // Make sure the value is not null
        if (value == null) {
            throw new NullPointerException();
        }

        // Makes sure the key is not already in the hashtable.
        Entry<?,?> tab[] = table;
        int hash = key.hashCode();
        int index = (hash & 0x7FFFFFFF) % tab.length;
        @SuppressWarnings("unchecked")
        Entry<K,V> entry = (Entry<K,V>)tab[index];
        for(; entry != null ; entry = entry.next) {
            if ((entry.hash == hash) && entry.key.equals(key)) {
                V old = entry.value;
                entry.value = value;
                return old;
            }
        }

        addEntry(hash, key, value, index);
        return null;
    }
```

### HastTable的get方法

```
	public synchronized V get(Object key) {
        Entry<?,?> tab[] = table;
        int hash = key.hashCode();
        int index = (hash & 0x7FFFFFFF) % tab.length;
        for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
            if ((e.hash == hash) && e.key.equals(key)) {
                return (V)e.value;
            }
        }
        return null;
    }
```

### HashMap深究

jdk8更新内容为：HashCode相同的组成链表==》当链表长度大于8时，转化成红黑树

- ***put方法***

![put详细图](hashmap_put.png)

- ***get方法***


### HashMap为什么两倍扩容

1. 为了hash的平均分布，table数组利用率更高
2. 为了扩容时数据更好的迁移

***第一个原因解释*** 我们来分析key如何转化为table[]数组的index

```
// 除去扩容，链表，树等知识点的伪代码
void put( key, value ){
	int hash = hash( key );
	Node node = new Node( hash, key, value );
	int length = table.length;
	// ( length - 1 ) & hash 这是个关键
	int index = ( length - 1 ) & hash;
	table[index] = node;
}
```

length 如果是 2的倍数，那么 length - 1 二进制表达式里面 1 是最多的。因此hash就表示最均匀，table利用率最高 

***第二个原因解释*** 我们来分析resize方法

```
// 我仍然写一些伪代码
void resize(){
	oldCap, newCap;
	newCap = oldCap << 1;
	
	node[] newtable = new node[newCap];
	
	// 下面将 table 中的元素复制进 newtable
	// 为什么直接 复制呢，就普通的Array复制，因为hash对应的index变了，可以通过原因1看出来
	
	for( int j=0; j<oldCap; ++j ){
		if( table[j] != null ){
			// 假设node通过list链接
			node e = table[j];
			//我们肯定会想接下来便利 node 链表，将其中的节点插入到新index对应的链表中。可是我们这时候就用到二倍关系了
			node l1, l2;
			for(){
				if( ( oldCap & e.hash ) > 0 ){
					//插入到l1
				}else{
					// 插入到l2
				}
			}
			//接下来将l1，l2中元素插入新的位置
			//下面就是我要解释的，为什么不用 index = hash( key ) & ( newCap - 1 )
			newtable[j] = l2;
			newtable[j+oldCap] = l1;
		}
	}
}
```

因为oldCap是2^n的关系，那么 如果hash的从右数第n为为1时，那么index肯定为 oldIndex + oldCap。hash第n位为0时，那么index肯定为 oldIndex。

因此cap为2的倍数递增，就可以减少扩容时数据的迁移时的时间消耗


