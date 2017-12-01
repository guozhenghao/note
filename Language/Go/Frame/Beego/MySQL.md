# Beego MySQL连接
- 使用beego的orm
````
go get github.com/astaxie/beego/orm
````
- 连接
````
func init() {
	//从config文件中读取
	dbhost := beego.AppConfig.String("dbhost")
	dbport := beego.AppConfig.String("dbport")
	dbuser := beego.AppConfig.String("dbuser")
	dbpassword := beego.AppConfig.String("dbpassword")
	dbname := beego.AppConfig.String("dbname")
	connectStr := dbuser + ":" + dbpassword + "@tcp(" + dbhost + ":" + dbport + ")/" + dbname + "?charset=utf8"
	orm.RegisterDataBase("default", "mysql", connectStr)
	//注册model
    //model就是结构体
	orm.RegisterModel(new(model名1), new(model名2))
}
````
- 配置文件样例
````
#程序名
appname = test

#版本号
AppVer = "1.0.0"

#监听端口号
httpport = 8181

###################### 数据库配置 ############################
# MYSQL地址
dbhost = 

# MYSQL端口
dbport = 

# MYSQL用户名
dbuser = 

# MYSQL密码
dbpassword = 

# MYSQL数据库名称
dbname = 
````
- 使用样例
````
var result []model名
//如果字符串转数字失败
if err != nil{
    fmt.Println(err.Error())
    return nil,err
}
if err != nil{
    fmt.Println(err.Error())
    return nil,err
}
//查询语句
o := orm.NewOrm()
sql := fmt.Sprintf("select * from table1")
//执行赋值
_, errr := o.Raw(sql).QueryRows(&result)
````