# GO语言MySQL
### 普通Go项目中使用方法
```go
package main

import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
	"log"
)

func GetDetail(id string)string {
    //链接
	db, err := sql.Open("mysql", "用户名:密码@tcp(IP:端口)/库名?charset=utf8")
	//如果涉及到时区则为：
	//库名?charset=utf8&loc=Asia%2FShanghai
	
	if err != nil {
		log.Fatalf("Open database error: %s\n", err)
	}
	defer db.Close()
	err = db.Ping()
	if err != nil {
		log.Fatal(err)
	}
    //查询语句
	rows, err := db.Query("select name from test_table where id = ?", id)
	if err != nil {
		log.Println(err)
	}
	defer rows.Close()
	var name string
    //得到值
	for rows.Next() {
		err := rows.Scan(&name)
		if err != nil {
			log.Fatal(err)
		}
        //返回
		return name
	}
	err = rows.Err()
	if err != nil {
		log.Fatal(err)
	}
	return "nil"

}

func main(){
	name :=  GetDetail("idididididid")
	fmt.Println(name)
}

```
### Beego中使用方法
- 使用beego的orm

`go get github.com/astaxie/beego/orm`

- 连接
```go
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
```
- 配置文件样例
```yaml
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
```
- 使用样例
```go
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
```
- 标准增删改查
```go
//das_info是个das信息的结构体
const (
	DAS_INFO_TABLE = "das_info"
)

func (this *DasInfo) TableName() string {
	return DAS_INFO_TABLE
}

func (this *DasInfo) Insert() error {
	id, err := orm.NewOrm().Insert(this)
	if err != nil {
		return err
	}
	this.Id = int32(id)
	return nil
}

func (this *DasInfo) Read(fields ...string) error {
	if err := orm.NewOrm().Read(this, fields...); err != nil {
		return err
	}
	return nil
}

func (this *DasInfo) Update(fields ...string) error {
	if _, err := orm.NewOrm().Update(this, fields...); err != nil {
		return err
	}
	return nil
}

func (this *DasInfo) Delete() error {
	if _, err := orm.NewOrm().Delete(this); err != nil {
		return err
	}
	return nil
}
```
- 事务(未测)
```go
dbObj := orm.NewOrm()
err = dbObj.begin
sql = fmt.Sprintf("insert into studentinfo(Id, Stuname, Stuidentify, Stubirth, Stuclass, Stumajor)"+" values(3, 'loe','xxx319918xxx','%s','zzzz','TTTT')", "1992-01-01 11:11:11")
_, err = dbObj.Raw(sql).Exec()
if err != nil {
	dbObj.Rollback()
	fmt.Println("插入t_studentInfo表出错,事务回滚")
} else {
	dbObj.Commit()
	fmt.Println("插入t_studenInfo表成功,事务提交")
}
```