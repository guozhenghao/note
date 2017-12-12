# Go语言Rand方法
- 使用方法有些复杂，以后在总结，先留坑

````
rand.New(rand.NewSource(time.Now().Unix())).Intn(1000000)
````