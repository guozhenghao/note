# Json相关
这次我使用的是google的gson
### 导入依赖
```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.2</version>
</dependency>
```
### 使用
常用的方法可以网上查

- java对象转json
```java
Gson gson = new Gson();
String json = gson.toJson(创建好的对象);
```

- json转对象
```java
Gson gson = new Gson();
对象 test = gson.fromJson(json字符串, 对象.class);
```