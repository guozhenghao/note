# Go语言protobuf使用

## 下载相关依赖
- 下载protoc

    下载地址为`https://github.com/google/protobuf/releases`，安装对应的版本

    将下载内容解压，并把bin目录添加到path中

- 安装相关依赖库

    `go get -u github.com/golang/protobuf/{protoc-gen-go,proto}`

    添加%GOPATH/bin到path中，protoc-gen-go提供go的相关内容，添加到path中protoc才使用到

## 使用
- test.proto
```c++
syntax = "proto3";

package testProto;

message UserInfo{
    string message = 1;   //消息
    int32 length = 2;    //消息大小
    int32 cnt = 3;      //消息计数
}
```
使用命令`protoc --go_out=. *.proto`生成对应的proto文件test.pb.go

- main.go
```go
package main
import (
	"fmt"
	"github.com/golang/protobuf/proto"
	"testProto"
)

func main(){

	testProto := &testp.UserInfo{
		Message:"sdfsdf",
	}

	fmt.Println(testProto.GetMessage())

	date,_ := proto.Marshal(testProto)
	fmt.Println(len(date))

	unTestProto := &testp.UserInfo{}
	_ = proto.Unmarshal(date,unTestProto)
	fmt.Println(unTestProto.Message)
}
```

## 备注
在使用上方protoc时候，service中的rpc方法等，没有regist方法。

使用`protoc --go_out=plugins=grpc:. *.proto`才会产生

如果proto中有import，在编译时package的import使用`-I`参数指定目录

常规grpc编译：
```shell
protoc -I/usr/local/include -I. \
  -I$GOPATH/src \
  -I$GOPATH/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
  --go_out=plugins=grpc:. \
  path/to/your_service.proto
  # 或者按照上方中写的带grpc的简单方法
```

设置rest的grpc编译：

`go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway`
```shell
protoc -I/usr/local/include -I. \
  -I$GOPATH/src  -I$GOPATH/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
  --grpc-gateway_out=logtostderr=true:. \
  path/to/your/service.proto
  # 生成.pb.gw.go文件
```

生成swagger：

`go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-swagger`
```shell
protoc -I/usr/local/include -I. \
  -I$GOPATH/src \
  -I$GOPATH/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
  --swagger_out=logtostderr=true:. \
  path/to/your_service.proto
  # 生成.swagger.json文件
```

[点击进入goproto的github地址](https://github.com/golang/protobuf)