
# go语言

### import和package

```
package main
import "fmt"
func main(){
    fmt.Println( "123" )
}
```
分析上面的代码

我们有package关键字，有一个要求，同一个文件夹下package对应的包名必须唯一,_test文件可以加上 _test包名

import关键字，你认为它引入的是包名对吧？其实不然，是文件夹名。我们可以尝试修改go源文件src的fmt文件夹名字至fmt1，这时找不到"fmt"了。因此import关键字引入的是goroot和gopath下面的文件夹，也可以绝对路径哦。然后文件内使用的使用如果import没有设置别名，使用时用package定义的包名

### strings

字符串处理包，位于$GOROOT/src/strings文件夹。可以阅读源码，此处简要说明近期遇到的func函数

```
strings.Contains( a, "12" )

strings.Index(a, "12")

strings.Compare( a, b )

strings.Split(a, "1")
```

### string的变量

```
var a string = nil // error
```
string的变量不能为nil

```
var a = "阿拉123"
fmt.Println( len(a) ) // 输出9。可以见汉字占4个字节

var b = []rune(a)
for _, v := range b {
    fmt.Println( v )
}
fmt.Println( len(b) ) //输出5
```

### strconv

```
//int to string
strconv.Itoa(v)
//100的16进制
strconv.FormatInt( 100, 16 )
// string to int
k, _ := strconv.Atoi("1")
//16进制的64转成int，第三个参数返回结果的bit大小 也就是int8 int16 int32 int64
strconv.ParseInt("64", 16, 0)
```

### sort

```
import "sort"
var a []int
sort.Ints(a) //升序
sort.Reverse( []type ) //反转
sort.Sort([]interface)
```

sort排序struct，需要让struct实现sort接口，三个方法.Len,Less,Swap

```
type Node struct{
    a int
}
type NodeList []Node
func (p NodeList)Swap(i,j int){ t:=p[i];p[i]=p[j];p[j]=t; }
func (p NodeList)Len()int { return len(p) }
func (p NodeList)Less(i,j int)bool {return p[i].a < p[j].a}
//Less函数返回true的话i位置在前，因此上面是升序排序

var ax NodeList
ax = append( ax, Node{1} )
ax = append( ax, Node{2} )
ax = append( ax, Node{-1} )
sort.Sort(ax)
```

### []Slice
切片,浅复制

[]Slice由数组指针，长度，容量构成

```
var a []int = make([]int, 5)
var b = a
a[4] = 5
fmt.Println( b[4] )
//上面输出为5.这表明了复制了基本变量，当出现扩容时可能会改变地址，因此可能改变不一致
```

### map[type]type

map数据结构不能排序，遍历的顺序并没有按照插入的顺序

因此我们日常有三种需求，按照插入顺序，按照key顺序，按照value顺序

插入顺序需要维持key的数组

key顺序也是维持key的数组，然后Sort(Key)

value顺序需要维持一个struct，下面的例子

map类型其实是一个指针，因此map间赋值都是浅复制

```
//按key排序
a := make(map[int]int)
a[1] = 2
a[3] = 4
a[2] = 6
var x []int
for k, v := range a{
    x = append( x, k )
}
sort.Ints(x)
for _, v := range x {
    fmt.Printf( "key:%d-->value:%d\n", v, a[v] )
}
```

```
//按照value排序
type Node struct {
    key int
    value int
}
type NodeList []Node
func (p NodeList)Swap(i,j int){ t:=p[i];p[i]=p[j];p[j]=t; }
func (p NodeList)Len()int { return len(p) }
func (p NodeList)Less(i,j int)bool {return p[i].value < p[j].value}

a := make( map[int]int )
a[1] = 2
a[2] = 4
a[4] = 1
a[3] = 10
var x NodeList
for k, v : range a {
    x = append( x, Node{ key:k, value:v } )
}
sort.Sort( x )
for _, v := range x {
    fmt.Printf( "key:%d-->value:%d\n", x.key, x.value )
}
```