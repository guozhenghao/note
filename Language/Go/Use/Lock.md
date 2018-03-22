# Go语言读写锁

- RWMutex提供四个方法：
    - func (*RWMutex) Lock //写锁定
    - func (*RWMutex) Unlock //写解锁
    - func (*RWMutex) RLock //读锁定
    - func (*RWMutex) RUnlock //读解锁

- WaitGroup提供有三个方法
	- Add(i int):添加或者减少等待goroutine的数量
	- Done():相当于Add(-1)
	- Wait():执行阻塞，直到所有的WaitGroup数量变成0

### RWMutex读锁
- 代码示例
```go
package main

import (
	"sync"
	"time"
)

var m *sync.RWMutex

func main() {
    //创建锁
	m = new(sync.RWMutex)
	for i :=1 ;i < 11;i++{
        //循环10次，分别10个用户读
		go read(i)
	}
	time.Sleep(15 * time.Second)

}

func read(i int) {

	println(i, "read start")
	m.RLock()
	println(i, "reading")
	time.Sleep(1 * time.Second)
	m.RUnlock()
	println(i, "read end")

}
```
- 执行结果
````
1 read start
1 reading
2 read start
2 reading
3 read start
3 reading
4 read start
4 reading
5 read start
5 reading
6 read start
6 reading
7 read start
7 reading
8 read start
8 reading
9 read start
9 reading
10 read start
10 reading
2 read end
6 read end
5 read end
4 read end
3 read end
10 read end
1 read end
9 read end
8 read end
7 read end
````

  在1没有结束读时，2已经开始读了。所有人都可以随便读。

### RWMutex写锁
- 代码示例
```go
package main

import (
	"sync"
	"time"
)

var m *sync.RWMutex

func main() {

	m = new(sync.RWMutex)
	//写的时候什么都不能干
	for i := 1;i < 11;i++{
		go write(i)
	}
	time.Sleep(15 * time.Second)

}

func write(i int) {

	println(i, "write start")
	m.Lock()
	println(i, "writing")
	time.Sleep(1 * time.Second)
	m.Unlock()
	println(i, "write end")

}
```
- 执行结果
````
1 write start
1 writing
6 write start
3 write start
4 write start
5 write start
9 write start
7 write start
2 write start
8 write start
10 write start
1 write end
6 writing
6 write end
3 writing
3 write end
4 writing
4 write end
5 writing
5 write end
9 writing
9 write end
7 writing
7 write end
2 writing
2 write end
8 writing
8 write end
10 writing
10 write end
````
  可以看到，当没有写完的时候，其他人都不能读

### WaitGroup等待组

```go
package main

import (
    "fmt"
    "sync"
)
//定义一个等待组
var wp sync.WaitGroup 

func main(){
	
	for i := 0;i < 10;i++{
		//添加一个计数
		wp.Add(1) 
		go myPrint(i)
	}
    //阻塞直到所有任务完成
    wp.Wait() 
    fmt.Println("全部完成")

}

func myPrint(i int) {

	fmt.Println(i)
	//执行完成 较少计数
	wp.Down()

}
```