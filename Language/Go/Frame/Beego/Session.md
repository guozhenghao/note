# Beego 中添加自己的session验证

### redis连接池
````
package redis

import (
	"time"

	"github.com/astaxie/beego"
	"github.com/garyburd/redigo/redis"
)

var (
	db_addr     string = ""
	db_password string = ""

	Session_live_time int = 3600 //session过期时间 单位秒

	redisPool *redis.Pool //redis连接池
)

func init() {
	db_addr = beego.AppConfig.String("redis_addr")
	db_password = beego.AppConfig.String("redis_password")

	var err error
	Session_live_time, err = beego.AppConfig.Int("session_live_time")
	if err != nil {
		Session_live_time = 3600
	}
}

func RedisConnect(dbNum int) redis.Conn {
	conn, _ := redis.Dial("tcp", db_addr, redis.DialPassword(db_password), redis.DialDatabase(dbNum))
	return conn
}

//获取Redis连接池
func newRedisPool(server, password string) (*redis.Pool, error) {
	var err error
	return &redis.Pool{
		MaxIdle:     32,
		IdleTimeout: 180 * time.Second,
		Dial: func() (redis.Conn, error) {
			var c redis.Conn
			c, err = redis.Dial("tcp", server)
			if err != nil {
				return nil, err
			}

			if password != "" {
				if _, err = c.Do("AUTH", password); err != nil {
					c.Close()
					return nil, err
				}
			}

			return c, err
		},
		TestOnBorrow: func(c redis.Conn, t time.Time) error {
			_, err := c.Do("PING")
			return err
		},
	}, err
}

/*
获取redis数据库连接
*/
func GetRedisConnection() (redis.Conn, error) {
	if redisPool == nil {
		var err error
		redisPool, err = newRedisPool(db_addr, db_password)
		if err != nil {
			return nil, err
		}
	}

	return redisPool.Get(), nil
}

````
### session类
````
package user

import (
	"encoding/json"
	"errors"
	"fmt"
	"time"
	"xs-dataeye-server/g"

	"xs-dataeye-server/models/redis"

	rds "github.com/garyburd/redigo/redis"
)

const (
	SESSION_KEY_PREFIX = "dataeye"
)

/**
根据用户信息，创建sessionId
规则：
 code + passwd + date ==> md5加密 ==> sessionId
*/
func GetSessionId(userInfo *UserInfo) (string, error) {
	if userInfo == nil {
		return "", errors.New("用户信息为空")
	}

	t := time.Now()
	sessionString := fmt.Sprintf("%s-%s-%d", userInfo.Code, userInfo.Password, t.Day())

	return g.GetMD5(sessionString), nil
}

/**
保存session
*/
func SetSession(key string, value interface{}) error {
	conn, err := redis.GetRedisConnection()
	if err != nil {
		return err
	}
	defer conn.Close()

	valueStr, err := json.Marshal(value)
	if err != nil {
		return err
	}

	key = fmt.Sprintf("%s%s", SESSION_KEY_PREFIX, key)
	_, err = conn.Do("SET", key, valueStr, "EX", redis.Session_live_time)
	if err != nil {
		return err
	}

	return nil
}

/**
获取session
*/
func GetSession(key string) (interface{}, error) {
	conn, err := redis.GetRedisConnection()
	if err != nil {
		return nil, err
	}
	defer conn.Close()

	key = fmt.Sprintf("%s%s", SESSION_KEY_PREFIX, key)
	ret, err := rds.Bytes(conn.Do("GET", key))
	if err != nil {
		return nil, err
	}

	var userInfo UserInfo
	err = json.Unmarshal(ret, &userInfo)
	if err != nil {
		return nil, err
	}

	return userInfo, nil
}

/**
删除session
*/
func DeleteSession(key string) error {
	conn, err := redis.GetRedisConnection()
	if err != nil {
		return err
	}
	defer conn.Close()

	key = fmt.Sprintf("%s%s", SESSION_KEY_PREFIX, key)
	_, err = conn.Do("DEL", key)

	return err
}

/**
统计活跃session，即在线用户数量
*/
func GetLiveSessionCount() (int32, error) {
	conn, err := redis.GetRedisConnection()
	if err != nil {
		return 0, err
	}
	defer conn.Close()

	ret, err := rds.Strings(conn.Do("KEYS", fmt.Sprintf("%s*", SESSION_KEY_PREFIX)))
	if err != nil {
		return 0, err
	}

	fmt.Println(ret)

	return 0, nil
}

````
### 使用
````
SetSession(sessionId, userInfo)
DeleteSession(sessionId)
````