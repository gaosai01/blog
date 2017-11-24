# 两个有序数组合并为一个有序数组

已知：

A[] = [1,2,4,5,7,8,20];

B[] = [0,3,6,9,22];

求：

A与B合并成一个递增数组

### 方法一：

新建C数组，将A加入，将B加入，快排C数组。复杂度： (n+m)log(n+m)

```java
代码过于简单，此处不写了
```

此方法复杂度过高

### 方法二

新建C数组（长度:n+m），顺序读取A和B前面的最小值，不断加入C数组。复杂度：n+m

```java

public void solve(){
	List<Integer> a = new ArrayList<Integer>(10);
	a.push( 1 );a.push(2);a.push(10);
	List<Integer> b = new ArrayList<Integer>(10);
	b.push( 2 );b.push(11);
	List<Integer> c = new ArrayList<Integer>(10);
	int x, y;
	x = y = 0;
	for( int i=0; i<a.size()+b.size(); ++i ){
		if( x < a.size() && y < b.size() ){
			if( a[x] < b[y] ){
				c.push( a[x] );
				++x;
			}else{
				c.push(  b[y] );
				++y;
			}
		}else if( x < a.size() ){
			c.push( a[x] );
			++x;
		}else{
			c.push( a[y] );
			++y;
		}
	}
	
	for( int i=0; i<c.size(); ++i ){
		System.out.println( c[i] );
	}
}

```

### 方法三

此方法是对方法二的优化，非常依赖于数据存储方式

如果a数组长度为n+m的话,那么每次读取a和b的最大值，放到空着的最后面。这样节约了内存空间。性能和速度都挺优秀的.

```c

	void merge(int A[], int m, int B[], int n) {
        int count = m + n - 1;
        --m; --n;
        while (m >= 0 && n >= 0) A[count--] = A[m] > B[n] ? A[m--] : B[n--];
        while (n >= 0) A[count--] = B[n--];
    }

```

## 深入探索：n个有序数组排序

方法一：最小堆方法

思路：建立最小堆，将n个数组的首数字放入堆，取出堆中最小的数字加入C数组中，将数字对应的数组的下一个数字加入最小堆，如此循环。

由于最小堆维护，并且每次都是n个数字的取数，所以达到了优化。

方法二：分治+上述算法

两两合并，最终获取答案