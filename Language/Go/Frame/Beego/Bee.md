# Beego项目创建工具Bee
- 安装

`go get github.com/beego/bee`

安装完之后，bee 可执行文件默认存放在 $GOPATH/bin 里面，所以您需要把 $GOPATH/bin 添加到您的环境变量中，才可以进行下一步。


- 新建项目

new 命令是新建一个 Web 项目，我们在命令行下执行 bee new <项目名> 就可以创建一个新的项目。但是注意该命令必须在 $GOPATH/src 下执行。最后会在 $GOPATH/src 相应目录下生成如下目录结构的项目。

`bee new testProject `
