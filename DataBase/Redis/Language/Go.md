# Redis go语言操作
### 导入包
````
go get github.com/garyburd/redigo/redis
````
### 连接
````
package main

import (
	"github.com/garyburd/redigo/redis"
)

func RedisConnect() redis.Conn {
    db_addr := "ip:port"
    db_password := "密码"
    dbNum := 数据库号
	conn,_ := redis.Dial("tcp",db_addr,redis.DialPassword(db_password),redis.DialDatabase(dbNum))
	return conn

   // 没有密码等可以不指定
}

````

### 使用
````
conn := models.RedisConnect()
defer func() {
	conn.Close()
}()
_,err := conn.Do("SET",fmt.Sprintf("%s%d",mailAddr,randomNum),mailAddr,"EX",60 * 5)
// 若信息为永久储存，则不需要EX和时间（秒）
test,err := redis.String(conn.Do("GET","123456"))
````

### SET、GET 结构体(JSON)
- SET
````
value, err := json.Marshal(结构体)
通过SET直接添加即可
````
- GET
````
valueBytes, err2 := redis.Bytes(conn.Do("GET", key值))
json.Unmarshal(valueBytes, 结构体)  
````
