# Go语言WebServer
- 使用默认的handler，然后通过path来调用不同方法
````
package main

import (
	"fmt"
	"net/http"
	"strings"
	"log"
)

func sayHello(response http.ResponseWriter, request *http.Request) {
	request.ParseForm()
	for k, v := range request.Form {
		fmt.Println("key:", k)
		fmt.Println("val:", strings.Join(v, ""))
	}
	fmt.Fprintf(response, "Hello! ")
}

func sayBye(response http.ResponseWriter, request *http.Request) {
	request.ParseForm()
	for k, v := range request.Form {
		fmt.Println("key:", k)
		fmt.Println("val:", strings.Join(v, ""))
	}
	fmt.Fprintf(response, "Bye! ")
}

func main() {
	http.HandleFunc("/hello", sayHello)
	http.HandleFunc("/bye", sayBye)
	err := http.ListenAndServe(":9090", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
````

- 自己创建handler，然后实现跳转
````
package main

import (
	"fmt"
	"net/http"
	"strings"
	"log"
)

type MyHandler struct {
}

func (p *MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	if r.URL.Path == "/hello" {
		sayHello(w, r)
	}else if r.URL.Path == "/bye" {
		sayBye(w, r)
	}else{
		http.NotFound(w, r)
	}
	return 
}

func sayHello(response http.ResponseWriter, request *http.Request) {
	request.ParseForm()
	for k, v := range request.Form {
		fmt.Println("key:", k)
		fmt.Println("val:", strings.Join(v, ""))
	}
	fmt.Fprintf(response, "Hello! ")
}

func sayBye(response http.ResponseWriter, request *http.Request) {
	request.ParseForm()
	for k, v := range request.Form {
		fmt.Println("key:", k)
		fmt.Println("val:", strings.Join(v, ""))
	}
	fmt.Fprintf(response, "Bye! ")
}

func main() {
	myHandler := &MyHandler{}
	err := http.ListenAndServe(":9090", myHandler)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
````