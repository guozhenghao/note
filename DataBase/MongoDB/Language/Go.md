# Go语言使用mongodb
`go get gopkg.in/mgo.v2`
### 建立连接Demo
````
package mongo

import (
	"errors"
	"time"

	"gopkg.in/mgo.v2"
)

// 连接mongodb数据库
var (
	MongodbAddr   string = "" //mongodb数据库地址
	MongodbName   string = "" //mongodb数据名称
	MongodbUser   string = "" //mongodb用户名
	MongodbPasswd string = "" //mongodb密码
)

var (
	mongosession *mgo.Session
)

func init() {
	MongodbAddr = "ip:port"
	MongodbName = "数据库名"
	MongodbUser = 用户名
	MongodbPasswd = 密码
}

func GetMongoSession() *mgo.Session {
	if mongosession == nil {
		var err error

		if MongodbUser == "" || MongodbPasswd == "" {
			mongosession, err = mgo.Dial(MongodbAddr)
		} else {
			dialInfo := &mgo.DialInfo{
				Addrs:     []string{MongodbAddr},
				Direct:    false,
				Timeout:   time.Second * 30,
				Database:  MongodbName,
				Source:    "admin",
				Username:  MongodbUser,
				Password:  MongodbPasswd,
				PoolLimit: 4096, // Session.SetPoolLimit
			}

			mongosession, err = mgo.DialWithInfo(dialInfo)
		}

	
	}

	return mongosession.Clone()
}

func WithMongoCollection(collectionName string, s func(*mgo.Collection) error) error {
	session := GetMongoSession()
	if session == nil {
		return errors.New("获取mongodb连接失败")
	}
	defer session.Close()

	c := session.DB(MongodbName).C(collectionName)
	return s(c)
}

````

### 使用

- 连接类
````
package mydatabase

import (
	"time"

	"gopkg.in/mgo.v2"
)

func GetMongoSession(user,password,ip,port,databaseName string) *mgo.Session {
	var mongosession *mgo.Session
	dialInfo := &mgo.DialInfo{
		Addrs:     []string{ip+":"+port},
		Direct:    false,
		Timeout:   time.Second * 30,
		Database:  databaseName,
		Source:    "admin",
		Username:  user,
		Password:  password,
		PoolLimit: 4096, // Session.SetPoolLimit
	}
	mongosession, err := mgo.DialWithInfo(dialInfo)
	if err != nil {
		//g.Log.Error("连接mongodb失败:%s", err.Error())
		return nil
	}
	return mongosession
}

//使用时建立连接代码demo
db := mydatabase.GetMongoSession("用户名","密码","ip","port","库名")
c := db.DB("库名").C("表名")
````
#### 所有的操作都可以使用结构体或者bson，后面的有些操作就不分两种情况写了
- 插入
````
type Person struct {
	Name	string	`bson:"name"`
	Age 	int  `bson:"age"`
}
person1 := &Person{"zhang",1}
//通过结构体插入，bson直接插入
err := c.Insert(person1,bson.M{"name":"li","age":2})
if err != nil{
	fmt.Println(err.Error())
}
````

- 删除
````
person1 := &Person{"zhang",1}
//结构体
_, err := c.RemoveAll(person1)
//bson
_, err := c.RemoveAll(bson.M{"name":"li","age":2})
if err != nil{
	fmt.Println(err.Error())
}
````

- 修改
````
person1 := &Person{"li",2}
//直接将后面的部分整体替换前面部分
_,err := c.Update(bson.M{"name":"li"},bson.M{bson.M{"name":"qwer"})
//使用$set 将前面部分的对应字段进行修改
_,err := c.Update(bson.M{"name":"li"},bson.M{"$set":bson.M{"name":"qwer"}})
//上面两个update只能更新一条，updateall可以将匹配的全部替换，但是脾虚使用$set
_,err := c.UpdateAll(bson.M{"name":"li"},bson.M{"$set":bson.M{"name":"qwer"}})
if err != nil{
	fmt.Println(err.Error())
}
````
- 查询
````
var person2 Person
//One方法相当于findOne
err = c.Find(bson.M{"name": "li"}).One(&person2)
//查询多个结果用Iter方法
item := c.Find(bson.M{}).Iter()
for item.Next(&person2){
	fmt.Println(person2.Name)
}
````
- Pipe(aggregate)
````
person3 := Person{}
iter := c.Pipe([]bson.M{bson.M{"$match":bson.M{"name":"qwer"}},bson.M{"$sort":bson.M{"age":1}}}).Iter()
//数组中的每一项不写bson.M也行: []bson.M{{"$match":bson.M{"name":"qwer"}},{"$sort":bson.M{"age":1}}}
for iter.Next(&person3){
	fmt.Println(person3)
}
````
