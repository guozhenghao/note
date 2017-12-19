# Go语言使用mongodb
### 建立连接
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