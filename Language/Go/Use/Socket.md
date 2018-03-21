# Go语言Socket

### 监听
listener
```go
package main

import (
	"net"
	"fmt"
	"bufio"
)

func main() {
	//监听的地址
	service := "0.0.0.0:1200"
	//生成tcp地址 参数可为tcp4/tcp6/tcp
	tcpAddr, _ := net.ResolveTCPAddr("tcp4", service)
	//监听器
	listener, _ := net.ListenTCP("tcp", tcpAddr)
	for {
		//不断监听
		conn, err := listener.Accept()
		if err != nil {
			continue
		}
        //处理
		go handleClient(conn)
	}
}

func handleClient(conn net.Conn) {
	//读
	r := bufio.NewReader(conn)
	for {
		//拆包
        //结束符是\n，对应ascii码是10
		line, err := r.ReadBytes(byte(10))
		if err != nil {
			break
		}
		//信息类型验证 得到发送的地址
		fmt.Printf("%v \n",conn.RemoteAddr())
		fmt.Println(line)
		//返回信息
		conn.Write([]byte("返回的信息 \n"))
	}
}
```
### 发送
sender
```go
package main

import (
	"fmt"
	"net"
	"os"
	"bufio"
)

func main() {
	//建立连接地址
	service := "127.0.0.1:1200"
	//生成tcp地址
	tcpAddr, _ := net.ResolveTCPAddr("tcp4", service)
	//建立链接
	conn, _ := net.DialTCP("tcp", nil, tcpAddr)
	//写入发送信息
	conn.Write([]byte("发送的信息 \n"))
	//读
	r := bufio.NewReader(conn)
	for {
		//拆包
		line, err := r.ReadBytes(byte(10))
		if err != nil {
			break
		}
		//信息类型验证 查看发送地址
		fmt.Printf("%v \n",conn.RemoteAddr())
		fmt.Println(line)
	}
	os.Exit(0)
}

```