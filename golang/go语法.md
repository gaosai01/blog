

## Golang语法


#### 变量

```
var a int
var b int = 5
a = 6;
c := 7;
var (
    d int = 5
    e int = 6
)
var f map[int]string = make( map[int]string )
var g []int = make( []int, len, cap )
```

#### 常量

```
const a int = 6
```


以上常量变量如果是全局变量，那么可以在其他包中通过import包，包.变量的方式使用

#### 数组

```
var balance [10] float32

//初始化数组

var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}

//多维数组
var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type
```

#### 指针

```
// 变量所在地址
var b = 5
var a *int = &b
fmt.Println( "pointer address : %d", a )
fmt.Println( "value : %d", *a )

//指针可作为函数的参数传递
```

#### 动态数组，切片，列表

```
//切片不需要声明长度也可使用
var a []int
//或者使用make创建
var b = make( []int, len, cap )
// len() 和 cap() 函数
fmt.Println( "list len %d, cap %d", len(b), cap(b) )
// append
append( b, 100 )
//copy函数，将b内容复制到a中
copy( a, b )
// copy从b的index1开始（等价与第二个元素）
copy( a, b[1:] )

// b[1:] 这句话是通过b创建新的切片的语法。

for _, value := range b[1:] {
    
}

// struct的数组。深复制还是浅复制呢？

```

#### Map集合

```
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
map_variable := make(map[key_data_type]value_data_type)
/* 查看元素在集合中是否存在 */
val, ok := map_variable["United States"]
/* 如果 ok 是 true, 则存在，否则不存在 */
if(ok){
    fmt.Println("exists", captial) 
}else {
    fmt.Println("not exists") 
}
//delete() 函数 
delete( map_variable, "key" )
//len() 函数
var size = len( map_variable )
```


#### if

```
if true {
    fmt.Println( "true" )
} else {
    fmt.Println( "false" )
}

if 表达式; true {
    fmt.Println( "true" )
}

```

#### for

```
for i:=1; i<10; i += 1 {
    fmt.Println( i )
}

for true {
    fmt.Println( "死循环" )
}

// 遍历列表
for index, value := range list {
    fmt.Println( "index: %d, value: %d", index, value )
}
for _, value := range list {
    
}
// 遍历map
for key := range map {
    fmt.Println( "key: %s, value: %s", key, map[key] )
}
for key, value := range map {
    fmt.Println( "key: %s, value: %s", key, value )
}
// 遍历字符串
for i, c := range "golang" {
    fmt.Println(i, c)
}
```

注意go语言没有while循环,，但是可以用for替换

#### func

```
//多个返回值，并且返回值为局部变量
func method_name( a int, b int ) ( c int, d int ){
    c = b
    d = a
}
//多个返回值，返回值没有名字
func method_name( a int, b int ) ( int, int ){
    return b, a
}
// 定义函数结构
type method_type func( int )

var b method_type = func ( a int ){
    fmt.Println( a );
}
b( 5 )
//func可以作为函数参数
```
#### struct

```
type struct_name struct {
    b int
    c int
}
//struct的内部方法，大写代表公共放大，小写代表只能当前包可见
func ( bean struct_name ) sum() int{
    return bean.b + bean.c
}
//无需new或者make，默认不是nil
var b struct_name
b.b = 5
b.c = 6
fmt.Println( b.sum() )
// 另一种初始化方式
var v = struct_name{ 1, 2 }
fmt.Println( v.sum() )
// 另一种初始化方式
var x = struct_name{ 
    b : 1,
    c : 2 }
fmt.Println( x.sum() )
```

#### interface

```
type iface_name interface {
    Method_name( int )int
}
//如何实现接口，类似于java中的struct implents interface
//只需要让struct结构体实现interface的全部方法即可

func ( b struct_name) Method_name( c int )int{
    return b.b + b.c + c
}
var iface iface_name
var b struct_name
iface = b

```

#### {} new make 都是初始化struct结构提体的

1、make只能用来分配及初始化类型为slice，map，chan的数据；new可以分配任意类型的数据

2、new分配返回的是指针，即类型*T；make返回引用，即T；

3、new分配的空间被清零，make分配后，会进行初始化

4、对于struct的分配和初始化，除了可以使用new外，还可以这样做： T {}

#### T{}初始化变量需要注意地方
1、与C语言不同，T{}分配的局部变量是可以返回的，且返回后该空间不会释放，例如
```
import "fmt"
type T struct {
    i, j int
}
func a(i, j int) T {
    i := T { i, j}
    return i
}
func b {
    t = a(1, 2)
    fmt.Println(t)
}
```
2、一个语法问题
使用T{}来初始化时，符号}不能单独占一行，否则会报错，如下：
```
missing ',' before newline in composite literal
或者

syntax error: need trailing comma before newline in composite literal
non-declaration statement outside function body
syntax error: unexpected }
```


#### 伪继承
go的struct无法实现真正的oo继承，单继承，多继承
但是提供语法糖，然而仍然父类对象=子类对象这样的语句仍然不可使用
只能使用接口实现多态
```
type A struct {
    b int
}
func (a A) Print(){
    fmt.Println( a.b )
}

type B struct{
    A
}
//这样就可以调用B.Print方法了
//然而仍然无法有var a A = B{}这样的语句，强制类型转化也不行
```