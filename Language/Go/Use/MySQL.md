# Go语言链接mysql
````
package main

import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
	"log"
)

func GetDetail(id string)string {
    //链接
	db, err := sql.Open("mysql", "用户名:密码@tcp(IP:端口)/库名?charset=utf8")
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


````