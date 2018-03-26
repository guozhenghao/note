# Go 定时任务 Cron
- 导包
```go
go get github.com/robfig/cron
```
- Cron

    cron表达式与linux的相同 可以直接百度搜索

- 使用

    该定时库有许多方法，先写一种用法，以后再补
```go
func main(){
	c := cron.New()
	spec := "0 */30 * * * ?"
	c.AddFunc(spec, func() {
		fmt.Println(time.Now().Format("2006-01-02 15:04:05"))
	})
	c.Start()
}
```