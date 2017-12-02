# Go语言反射

- Go的reflect库有两个重要的类型：
    - reflect.Type
    - reflect.Value

  Type,Value分别对应对象的类型和值数据

- 两个重要的函数：
    - TypeOf
        - reflect.TypeOf(i interface{}) Type
        - reflect.TypeOf()返回值的类型就是reflect.Type
    - ValueOf
        - reflect.ValueOf(i interface{}) Value
        - reflect.ValueIOf()返回值的类型就是reflect.Value

### reflect.Type
##### reflect.TypeOf(i interface{}) Type
- 因为reflect.Typeof的参数是空接口类型，因此可以接收任意类型的数据。 TypeOf()的返回值是这个接口类型对应的reflect.Type对象。通过Type提供的一些方法，就可以获得这个接口实际的静态类型。
````
import (
  "fmt"
  "reflect"
)

type Foo struct {
  X string
  Y int
}

func main() {
  var i int = 123
  var f float32 = 1.23
  var l []string = []string{"a", "b", "c"}

  fmt.Println(reflect.TypeOf(i))    //int
  fmt.Println(reflect.TypeOf(f))    //float32
  fmt.Println(reflect.TypeOf(l))    //[]string

  var foo Foo
  fmt.Println(reflect.TypeOf(foo))    //main.Foo

}
````
- 查看reflect包的源代码可以看到，reflect.Type的定义如下：
````
type Type interface {
  Align() int
  FieldAlign() int
  Method(int) Method
  MethodByName(string) (Method, bool)
  NumMethod() int
  Name() string
  String() string
  Elem() Type
  Field(i int) StructField
  FieldByName(name string) (StructField, bool)
  Len() int
  .....
}
````
- 可见reflect.Type是一个接口类型的对象，这个接口包含了很多方法，像Name(),Field(),Method()等，下面再通过实例来了解几个比较重要的方法。
````
type Foo struct {
  X string
  Y int
}

func (f Foo) do() {
  fmt.Printf("X is: %s, Y is: %d", f.X, f.Y)

}

func main() {
  var s string = "abc"
  fmt.Println(reflect.TypeOf(s).String()) //string
  fmt.Println(reflect.TypeOf(s).Name())   //string

  var f Foo
  typ := reflect.TypeOf(f)
  fmt.Println(typ.String()) //main.Foo
  fmt.Println(typ.Name())     //Foo ，返回结构体的名字

}
````

- 上面的例子可见，通过Type.String(),Type.Name()方法就可以获得接口对应的静态类型。 下面几个方法，显示了Type的更多功能，特别是对于结构体对象而言。

- Field相关的方法
````
var f Foo
typ := reflect.TypeOf(f)
for i := 0; i < typ.NumField(); i++ {
  field := typ.Field(i)
  fmt.Printf("%s type is :%s\n", field.Name, field.Type)
}

//x type is :string
//y type is :int

field2, _ := typ.FieldByName("x") //等价于typ.Field(0)，返回的也是StructField对象
fmt.Println(field2.Name)          // x
````
- Type的Field是一个StructFiled对象：
````
type StructField struct {
    Name    string
    PkgPath string

    Type      Type      // field type
    Tag       StructTag // field tag string
    Offset    uintptr   // offset within struct, in bytes
    Index     []int     // index sequence for Type.FieldByIndex
    Anonymous bool      // is an embedded field
}
````
- Method相关的方法
````
var f Foo
typ := reflect.TypeOf(f)

fmt.Println(typ.NumMethod()) //1， Foo 方法的个数
m := typ.Method(0)
fmt.Println(m.Name) //do
fmt.Println(m.Type) //func(main.Foo)
fmt.Println(m.Func) //<func(main.Foo) Value>, 这个返回的是reflect.Value对象，后面再讲
````
- Kind方法Type和Value都有，它返回的是对象的基本类型，例如int,bool,slice等，而不是静态类型。
````
var f = Foo{}
typ := reflect.TypeOf(f)
fmt.Println(typ)        //main.Foo
fmt.Println(typ.Kind()) //struct

var f2 = &Foo{}
typ2 := reflect.TypeOf(f2)
fmt.Println(typ2)        //*main.Foo
fmt.Println(typ2.Kind()) //ptr
````
- kind()的返回值如下：
````
const (
  Invalid Kind = iota
  Bool
  Int
  Int8
  Int16
  Int32
  Int64
  Uint
  Uint8
  Uint16
  Uint32
  Uint64
  Uintptr
  Float32
  Float64
  Complex64
  Complex128
  Array
  Chan
  Func
  Interface
  Map
  Ptr
  Slice
  String
  Struct
  UnsafePointer
)
````

### reflect.Value
##### reflect.ValueOf(i interface{}) Value
- reflect.ValueOf()的返回值类型为reflect.Value,它实现了interface{}参数到reflect.Value的反射
````
type Foo struct {
  X string
  Y int
}

func (f Foo) do() {
  fmt.Printf("X is: %s, Y is: %d", f.X, f.Y)

}

func main() {
  var i int = 123
  var f = Foo{"abc", 123}
  var s = "abc"
  fmt.Println(reflect.ValueOf(i)) //<int Value>
  fmt.Println(reflect.ValueOf(f)) //<main.Foo Value>
  fmt.Println(reflect.ValueOf(s)) //abc
  //Value.String()方法对string类型的数据做了特殊处理，会直接返回字符串的值。
  //其它类型对象返回的格式都是"<Type% Value>"
}
````
- reflact.Value对象可以通过调用Interface()方法，再反射回interface{}对象
````
              reflect.ValueOf()                        Interface()
interface{} ---------------------> reflect.Value -------------------> interface{}

var i int = 123
fmt.Println(reflect.Valueof(i).Interface()) //123

var f = Foo{"abc", 123}
fmt.Println(f) //{abc 123}
fmt.Println(reflect.ValueOf(f).Interface() == f)  //true
fmt.Println(reflect.ValueOf(f).Interface())  //{abc 123}
````
- Filed方法

  和Type的Filed方法不一样，Type.Field()返回的是StructFiled对象，有Name,Type等属性，Value.Field()返回的还是一个Value对象。
````
var foo = Foo{"abc", 123}

val := reflect.ValueOf(foo)
fmt.Println(val.FieldByName("y")) //<int Value>  interface.Value对象

typ := reflect.Typeof(foo)
fmt.Println(typ.FieldByName("y")) //{  <nil>  0 [] false} 
````
````
func main() {
  var f = Foo{"abc", 123}
  rv := reflect.ValueOf(f)
  rt := reflect.TypeOf(f)
  for i := 0; i < rv.NumField(); i++ {
      fv := rv.Field(i)
      ft := rt.Field(i)
      fmt.Printf("%s type is :%s ,value is %v\n", ft.Name, fv.Type(), fv.Interface())
  }
}

//X type is :string ,value is abc
//Y type is :int ,value is 123
````
- 设置Value的值

  要设置reflect.Value的值还颇费周折，不能直接对Value进行赋值操作

````
var s = "abc"
fv := reflect.ValueOf(s)
fmt.Println(fv.CanSet()) //false
// fv.SetString("edf")   //panic

fv2 := reflect.ValueOf(&s)
fmt.Println(fv2.CanSet()) //false
// fv2.SetString("edf")      //panic
````
- relect.Value是字符s的一个反射对象，是不能直接对它进行赋值操作的。 要对s进行赋值，需要先拿到s的指针对应的reflect.Value,然后通过Value.Elem()再对应到s，然后才能赋值操作。 这个地方是相当拗口啊:(
````
func main() {
  var i int = 123
  fv := reflect.ValueOf(i)
  fe := reflect.ValueOf(&i).Elem()  //必须是指针的Value才能调用Elem
  fmt.Println(fe)       //<int Value>
  fmt.Println(fv)       //<int Value>
  fmt.Println(fv == fe) //false

  fmt.Println(fe.CanSet()) //true
  fe.SetInt(456)
  fmt.Println(i) //456

}
````
- Method

  这个是reflect一个比较经典的使用场景，在知道对象方法名的情况下，调用对象的方法。
````
type Foo struct {
  X string
  Y int
}

func (f Foo) Do() {
  fmt.Printf("X is: %s, Y is: %d\n", f.X, f.Y)

}

func main() {
  var foo = &Foo{"abc", 123}
  reflect.ValueOf(foo).MethodByName("Do").Call([]reflect.Value{})

}
//方法名Do必须是大写的，否则会抛异常
````

- reflect整体不是很好理解，如果要进一步掌握如何使用，以及在什么场景下用，建议看一些开源库的代码，来理解reflect的使用。下面几个库都大量使用了reflect，供参考：
    - web.go 
    - redigo