# Spring Boot 跨域解决方法
- 在controller的类文件前加上`@CrossOrigin`注解

e.g.

````
@Controller
@ResponseBody
@CrossOrigin
@RequestMapping(value = "test")
public class TestController {

}
````

## 跨域相关知识
- [什么是跨域和跨域访问](http://blog.csdn.net/notechsolution/article/details/50394391)
- [前端解决跨域问题的8种方案](http://blog.csdn.net/joyhen/article/details/21631833)
- [跨域的另一种解决方案——CORS跨域资源共享](http://www.cnblogs.com/shikyoh/p/4959562.html)
