# Go时间操作
- 先上个例子
````
package main

import (
"fmt"
"time"
)

func main() {
	//获取时间戳
	timestamp := time.Now().Unix()
	fmt.Println(timestamp)

	//格式化为字符串,tm为Time类型
	tm := time.Unix(timestamp, 0)
	fmt.Println(tm.Format("2006-01-02 03:04:05 PM"))
	fmt.Println(tm.Format("02/01/2006 15:04:05 PM"))

	//从字符串转为时间戳，第一个参数是格式，第二个是要转换的时间字符串
	tm2, _ := time.Parse("01/02/2006", "02/08/2015")
	fmt.Println(tm2.Unix())
}
````
- 在Go语言里，fomrat中的这些数字都是有特殊函义的，不是随便指定的数字，见下面列表：
    - 月份 1,01,Jan,January
    - 日　 2,02,_2
    - 时　 3,03,15,PM,pm,AM,am
    - 分　 4,04
    - 秒　 5,05
    - 年　 06,2006
    - 周几 Mon,Monday
    - 时区时差表示 -07,-0700,Z0700,Z07:00,-07:00,MST
    - 时区字母缩写 MST

- 使用时为了方便，可以直接：
````
tm := time.Now().Format("")
````