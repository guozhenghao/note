# UTC时间转换

UTC时间是Long型的时间，代表的是从1970年01月01日 00:00:00到现在一共有多少毫秒

- 工具类代码
```java
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.TimeZone;

public class MyDateTransf {
      public static void main(String[] args) {
          String time = trSecondToDate(Long. parseLong("1319990400" ));
          System. out .println(time );
           time = trSecondToDate(Long. parseLong("1320076799" ));
          System. out .println(time );
           long second = trDateToSecond( "2011-10-31 00:00:00");
          System. out .println(second );
     }
     //将UTC毫秒转换为正常日期
      public static String trSecondToDate(Long second) {
           if (second == null || second == 0) {
               return "" ;
          }
          Date da = null ;
           try {
               da = ( new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")).parse( "1970-01-01 08:00:00");
          } catch (Exception e ) {
               e.printStackTrace();
          }
          Date date = new Date(da .getTime() + second * 1000L);
           return ( new SimpleDateFormat( "yyyy-MM-dd HH:mm:ss")).format( date );
     }
     /将正常日期转换成毫秒
      public static long trDateToSecond(String date) {
          Date d = null ;
          SimpleDateFormat formater = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss" );
           formater.setLenient( false );
           try {
               d = formater.parse( date);
          } catch (ParseException e1 ) {
               e1.printStackTrace();
          }
           long stamp = 0L;
          Date date2 = null ;
           try {
               date2 = ( new SimpleDateFormat("yyyy/MM/dd HH:mm:ss")).parse( "1970/01/01 08:00:00");
               stamp = ( d.getTime() - date2.getTime()) / 1000L;
          } catch (Exception e ) {
               stamp = 0L;
          }
           return stamp ;
     }
}

```
