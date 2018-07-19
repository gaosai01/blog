
# go第四讲

### go程序读取启动时的参数

#### 方法1

os.Args会将执行程序名拿到，克自定定制解析工具

```
package main
 
import (
    "fmt"
    "os"
)
 
func main() {
    fmt.Println(os.Args)
}
./main -user="123"
outputs:
[./main -user="123"]
```

#### 方法2

goy语言自带了flag的命令行执行参数解析工具

```
执行时的参数格式为：
-v1=1
--v1=1
-v1 1
--v1 1
在go语言中如何拿到呢：
import "flag"
flag.Int( "v1", 0, "this is description" )
flag.String,flag.Bool
```

```
package main
 
import (
    "flag"
    "fmt"
)
 
func main() {
    id := flag.Int("id", 0, "id")
    port := flag.String("port", ":8080", "http listen port")
    var name string
    flag.StringVar(&name, "name", "123", "name")
 
    flag.Parse()
 
    fmt.Println("id:", *id)
    fmt.Println("port:", *port)
    fmt.Println("name:", name)
}
//go run main.go -port=8000 -name="golang"
```


### golang  etcd
api
https://studygolang.com/articles/9974

demo
https://www.cnblogs.com/sevenPP/p/8149890.html

### tcp链接池

https://github.com/silenceper/pool

https://studygolang.com/articles/349

https://github.com/henrylee2cn/teleport

### tcp 调优
https://studygolang.com/articles/5874