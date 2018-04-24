# Java时间相关操作
## 相关参数
````
yyyy：年  
MM：月  
dd：日  
hh：1~12小时制(1-12)  
HH：24小时制(0-23)  
mm：分  
ss：秒  
S：毫秒  
E：星期几  
D：一年中的第几天  
F：一月中的第几个星期(会把这个月总共过的天数除以7)  
w：一年中的第几个星期  
W：一月中的第几星期(会根据实际情况来算)  
a：上下午标识  
k：和HH差不多，表示一天24小时制(1-24)。  
K：和hh差不多，表示一天12小时制(0-11)。  
z：表示时区    
````
## 使用
- 获取当前时间并格式化
```
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String date = sdf.format(new Date());
```

- 将String时间转换为date
```java
Date date = new SimpleDateFormat("yyyy-MM-dd").parse("2011-01-01");
```

- 获取日期在年月日中的关系
```java
Calendar cal = Calendar.getInstance();
cal.setTime(new Date());
int week = cal.get(Calendar.WEEK_OF_YEAR);
//WEEK_OF_YEAR是查询是一年中的第几周，有许多的参数
```

- 时间加减
    - Date类型加减
    ```java
    SimpleDateFormat dd=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");     
    System.out.println(df.format(new Date(dateTime.getTime() + n * 24 * 60 * 60 * 1000L))); 
    //自己计算加减多少秒
    //注意这里一定要转换成Long类型，要不n超过25时会出现范围溢出，从而得不到想要的日期值 
    ```
    - 使用Calendar
    ```java
    Calendar calendar = Calendar.getInstance();
    calendar.setTime(new Date());
    calendar.add(Calendar.YEAR,-1);//日期减1年
    Date date = calendar.getTime()
    ```