# Go语言并发
### goroutine
- goroutine是通过Go的runtime管理的一个线程管理器。goroutine通过go关键字实现了，只能作用于方法。

### channels
- channel可以与Unix shell 中的双向管道做类比：可以通过它发送或者接收值。

  默认情况下，channel接收和发送数据都是阻塞的，除非另一端已经准备好，这样就使得Goroutines同步变的更加的简单，而不需要显式的lock。所谓阻塞，也就是如果读取（value := <-ch）它将会被阻塞，直到有数据接收。其次，任何发送（ch<-5）将会被阻塞，直到数据被读出。

```go
package main

import "fmt"

func sum(a []int, c chan int) {
	total := 0
	for _, v := range a {
		total += v
	}
	c <- total  // send total to c
}

func main() {
	a := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(a[:len(a)/2], c)
	go sum(a[len(a)/2:], c)
	x, y := <-c, <-c  // receive from c

	fmt.Println(x, y, x + y)
}
```


### Buffered Channels
- Go也允许指定channel的缓冲大小，很简单，就是channel可以存储多少元素。ch:= make(chan bool, 4)，创建了可以存储4个元素的bool 型channel。在这个channel 中，前4个元素可以无阻塞的写入。当写入第5个元素时，代码将会阻塞，直到其他goroutine从channel 中读取一些元素，腾出空间。
```go
package main

import "fmt"

func main() {
	c := make(chan int, 2)//修改2为1就报错，修改2为3可以正常运行
	c <- 1
	c <- 2
	fmt.Println(<-c)
	fmt.Println(<-c)
}
```
- 自己写的一个例子，避免上面的报错，又能显示移除这个的作用
```go
package main

import (
	"fmt"
	"time"
)

func main() {
	c := make(chan int, 1)
	for i := 0;i < 5;i++{
		go insert(i,&c)
	}
	delete(&c)
}
func insert(i int,c *chan int){
	*c <- i
	fmt.Printf("charu %d\n",i)
}
func delete(c *chan int){
	for i := 0;i < 5;i++{
		w := <- *c
		fmt.Printf("shanchu %d\n",w)
		time.Sleep(time.Second * 2)
	}
}
```
### Range和Close
- 可以通过range，像操作slice或者map一样操作缓存类型的channel
```go
package main

import (
	"fmt"
	"time"
)

func insert(c chan int) {

	for i := 0; i < 10; i++ {
		c <- i
		time.Sleep(time.Second * 5)
	}
	close(c)
	fmt.Println("关闭")
}

func main() {
	c := make(chan int, 10)
	go insert(c)
	for i := range c {
		fmt.Println(i)
	}
}
```

  for i := range c能够不断的读取channel里面的数据，直到该channel被显式的关闭。上面代码我们看到可以显式的关闭channel，生产者通过内置函数close关闭channel。关闭channel之后就无法再发送任何数据了，在消费方可以通过语法v, ok := <-ch测试channel是否被关闭。如果ok返回false，那么说明channel已经没有任何数据并且已经被关闭。

  应该在生产者的地方关闭channel，而不是消费的地方去关闭它，这样容易引起panic

  channel不像文件之类的，不需要经常去关闭，只有当你确实没有任何发送数据了，或者你想显式的结束range循环之类的

### Select
- Go里面提供了一个关键字select，通过select可以监听channel上的数据流动。

  select默认是阻塞的，只有当监听的channel中有发送或接收可以进行时才会运行，当多个channel都准备好的时候，select是随机的选择一个执行的。
```go
package main

import "fmt"

func insert(a,b, quit chan int) {

	for {
		select {
		case a <- 1:
		case b <- 2:
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	a := make(chan int)
	b := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-a)
		}
		quit <- 0
	}()
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-b)
		}
		quit <- 0
	}()
	insert(a,b, quit)
}
```
- 在select里面还有default语法，select其实就是类似switch的功能，default就是当监听的channel都没有准备好的时候，默认执行的（select不再阻塞等待channel）。
```go
select {
case i := <-c:
	// use i
default:
	// 当c阻塞的时候执行这里
}
```
### 超时
- 有时候会出现goroutine阻塞的情况，那么我们如何避免整个程序进入阻塞的情况呢？我们可以利用select来设置超时
```go
package main

import "time"

func main() {

	c := make(chan int)
	o := make(chan bool)
	go func() {
		for {
			select {
			case v := <-c:
				println(v)
			case <-time.After(5 * time.Second):
				println("timeout")
				o <- true
				break
			}
		}
	}()
	<-o
}

```
### runtime goroutine

- runtime包中有几个处理goroutine的函数：
    - Goexit

        退出当前执行的goroutine，但是defer函数还会继续调用

    - Gosched

        让出当前goroutine的执行权限，调度器安排其他等待的任务运行，并在下次某个时候从该位置恢复执行。

    - NumCPU

        返回 CPU 核数量

    - NumGoroutine

        返回正在执行和排队的任务总数

    - GOMAXPROCS
    
        用来设置可以并行计算的CPU核数的最大值，并返回之前的值。