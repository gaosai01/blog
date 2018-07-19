

### defer

golang中defer的执行

func函数return前执行defer

```
func call() int{
    for i := 0; i < 5; i++ {
      defer  fmt.Printf("%d ", i)
    }
    return 1
}
//调用call会输出4 3 2 1 0.
//defer是类似于栈，先进后出的形式
```

```
func deferExp(x, y int) (z int){
  defer func(){z += 2}
  z = x + y
  return z + 1 // 执行顺序 z=x+y -> z = z+1 ->z = z+2 -> return  
}

func main() {
  i := deferExp(1,1)
  fmt.Println(i)  // print 5
}
//输出了152
//上面是个事例，如果z为局部变量，结果会变
//defer主要用于读写流关闭，锁释放等业务
```

由于本人是做java开发，觉得此处和try{}finally{}结构很相似，说一下不同点

1. finally里面可以return，defer里面不可return，如需返回需要定义return值参数列表，就上上面的z参数
2. defer是函数块执行完毕后执行的。

### panic和recover

另一种异常处理机制

panic又名运行时恐慌，调用panic方法会跑出异常，当前函数终止执行，按照函数栈的调用顺序向上回退，一直到defer func上的recover函数处理

```
package main

import (
	"errors"
	"fmt"
)

func innerFunc() {
	fmt.Println("Enter innerFunc")
	panic(errors.New("Occur a panic!"))
	fmt.Println("Quit innerFunc")
}

func outerFunc() {
	defer func() {
		if p := recover(); p != nil {
			fmt.Printf("Fatal error: %s\n", p)
		}
	}()
	fmt.Println("Enter outerFunc")
	innerFunc()
	fmt.Println("Quit outerFunc")
}


func main() {
	fmt.Println("Enter main")
	outerFunc()
	fmt.Println("Quit main")
}
```

### interface{} 与 nil 的故事

这里简要描述一下个人理解，这个问题很抽象，需要阅读大量文档

nil是interface{}对象

interface{}底层是由(type,value)对构成

interface{}对象==nil时需要type=nil和value=nil

个人总结的一句话:interface{}变量被赋值时，会同时赋值type和value。如果type非interface{}，但是value为nil，仍然对象不等于nil。例1就是最好的解释

```
// 例1
package main
import (
	"fmt"
	"reflect"
)
func main() {
	var val interface{} = (*interface{})(nil)
	if val == nil {
		fmt.Println("val is nil")
	} else {
		fmt.Println( reflect.TypeOf( val ) )
		fmt.Println("val is not nil")
	}
}
//例2
package main
import (
	"fmt"
	"reflect"
)
func main() {
	var val interface{} = nil
	if val == nil {
		fmt.Println("val is nil")
	} else {
		fmt.Println( reflect.TypeOf( val ) )
		fmt.Println("val is not nil")
	}
}
//例3
package main
import (
	"fmt"
	"reflect"
)
func main() {
	var val interface{} = (int)(5)
	if val == nil {
		fmt.Println("val is nil")
	} else {
		fmt.Println( reflect.TypeOf( val ) )
		fmt.Println("val is not nil")
	}
}
//例4
package main
import (
	"fmt"
	"reflect"
)
func main() {
	var val interface{} = (interface{})(nil)
	if val == nil {
		fmt.Println("val is nil")
	} else {
		fmt.Println( reflect.TypeOf( val ) )
		fmt.Println("val is not nil")
	}
}
```
### interface{}与nil常见场景
最常见场景就是error类型的复制啦，请看代码，分析输出

```
package main

import (
	"fmt"
)

type data struct{}

func (this *data) Error() string { return "" }

func test() error {
	var p *data = nil
	return p
}

func main() {
	var e error = test()
	if e == nil {
		fmt.Println("e is nil")
	} else {
		fmt.Println("e is not nil")
	}
}
//output: e is not nil
//实际上e = ( *data, nil )
```

