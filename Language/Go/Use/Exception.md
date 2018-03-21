# Go语言异常处理、
- 主要方法：
    - panic()  相当于throw
    - recover()  相当于cathch
    - defer fun()  相当于finally

defer为必执行方法，当该方法执行完成或出现错误中断时，程序从该出向上寻找defer方法，所以一般将defer写在所需方法的开头。
```go
func test(){
    defer func(){
        // 判断程序是否有异常
        if err := recover();err!=nil{
            // 自定义逻辑处理
            fmt.Printf("%+v",err)
            // 抛出异常
            panic(err)
        }
    }()
    //test方法的操作
    b := []byte{10}
    fmt.Printf("%x",b[100])
}
```

