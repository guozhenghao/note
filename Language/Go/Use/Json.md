# Json相关
### encoding/json
- go语言自带方法
```go
//结构体转换为json，结果为比特流
value, err := json.Marshal(结构体/字符串)
//将json比特流转换为结构体
json.Unmarshal(valueBytes, 结构体) 
```
- 上述方法我只在redis相关操作中用过，可在---->[数据库分类中redis/go]((/DataBase/Redis/Language/Go.md))<----查看
### go-simplejson
- github.com/bitly/go-simplejson
```go
value,_ := simplejson.NewJson([]byte(字符串))
//get和set方法
value.Get("aaa").Get("bbb").GetIndex(0).Set("qqqq","asdfasdf")
```