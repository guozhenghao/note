# Go语言Rand方法
- go伪随机
```go
package main  
import (  
    "fmt"  
    "math/rand"  
)  
func main() {  
    for i := 0; i < 10; i++ {  
        fmt.Println(rand.Int())  
    }  
}  
```
不管运行多少次，都会发现每次的结果都一样

- 自己设置个种子，比如为10
```go
package main  
import (  
    "fmt"  
    "math/rand"  
)  
func main() {  
    rnd := rand.New(rand.NewSource(10))  
    for i := 0; i < 10; i++ {  
        fmt.Println(rnd.Int())  
    }  
}  
```
发现与上面第一段代码的结果不一样了，但是这段代码的运行结果每次都一样

看源码会发现，rand.Int()下的随机数都是以1位种子的，rand相当于默认的全局。所以要每次都不一样，就要改成活种。

- 活种
```go
package main  
import (  
    "fmt"  
    "math/rand"  
    "time"  
)  
func main() {  
    rnd := rand.New(rand.NewSource(time.Now().UnixNano()))  
    //rnd := rand.New(rand.NewSource(time.Now().Unix()))  
    for i := 0; i < 10; i++ {  
        fmt.Println(rnd.Int())  
    }  
}  
```
这样根据每次运行的时间设置种子就不一样了

- 设置固定位数的随机数（如6位）
```go
package main
import (
	"fmt"
	"math/rand"
	"time"
)
func main() {
	rnd := rand.New(rand.NewSource(time.Now().UnixNano()))
	vcode := fmt.Sprintf("%06v", rnd.Int31n(1000000))
	fmt.Println(vcode)
}
```