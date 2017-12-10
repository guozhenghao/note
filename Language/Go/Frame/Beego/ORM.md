# Beego ORM 数据库模块
- [ORM简单使用](https://beego.me/docs/mvc/model/orm.md#%E6%97%B6%E5%8C%BA%E8%AE%BE%E7%BD%AE)
- [SQL语句使用](https://beego.me/docs/mvc/model/rawsql.md)
- [高级查询QuerySeter](https://beego.me/docs/mvc/model/query.md)

## 连接
- 读config文件
````
import (
	"github.com/astaxie/beego"
	"github.com/astaxie/beego/orm"
	_ "github.com/go-sql-driver/mysql"
)
// 连接mysql数据库
func init() {
	dbhost := beego.AppConfig.String("dbhost")
	dbport := beego.AppConfig.String("dbport")
	dbuser := beego.AppConfig.String("dbuser")
	dbpassword := beego.AppConfig.String("dbpassword")
	dbname := beego.AppConfig.String("dbname")
	if dbport == "" {
		dbport = "3306"
	}
	connectStr := dbuser + ":" + dbpassword + "@tcp(" + dbhost + ":" + dbport + ")/" + dbname + "?charset=utf8"
	orm.RegisterDataBase("default", "mysql", connectStr)
	// 注册model
	orm.RegisterModel(new(一个model))
}
````

- 代码中写死，将上边connectStr改成自己的连接str就行就行
````
username:password@tcp(ip:port)/datebase_name?charset=utf8
````

#### 特别注意，如果涉及到数据库的时间，建议直接在这里写上时区，如：
````
username:password@tcp(ip:port)/datebase_name?charset=utf8&loc=Asia%2FShanghai
````

## 使用
- 结构体中的变量名不能瞎起，除首字母外，每遇到一个大写字母会将其变为小写然后前方加入下划线，然后将这个名字与数据库做对应：
````
UserName --> user_name
````