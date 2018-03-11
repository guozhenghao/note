# 判断点在面内的方法

- 使用的是maven项目，首先导入包，在pom文件中添加
```xml
<!-- https://mvnrepository.com/artifact/nl.cloudfarming.client/lib-geotools -->
< dependency>
     < groupId> nl.cloudfarming.client </groupId >
     < artifactId> lib- geotools</ artifactId>
     < version> 2.7.2.2</ version >
</ dependency>

```
- 工具类代码
```java
import org.geotools.geometry.jts.JTSFactoryFinder;
import com.vividsolutions.jts.geom.Coordinate;
import com.vividsolutions.jts.geom.GeometryFactory;
import com.vividsolutions.jts.geom.Point;
import com.vividsolutions.jts.geom.Polygon;
import com.vividsolutions.jts.io.ParseException;
import com.vividsolutions.jts.io.WKTReader;

public class GeometyMethod {
      private GeometryFactory geometryFactory = JTSFactoryFinder.getGeometryFactory( null );
      //判断点是否在面内
      public boolean withinGeo( double x, double y ,Polygon polygon ) throws ParseException{
          Coordinate coord = new Coordinate(x ,y );
          Point point = geometryFactory .createPoint( coord );
           //        WKTReader reader = new WKTReader( geometryFactory );
           //        Polygon polygon = (Polygon) reader.read(geometry);
           return polygon .contains( point);
     }
      //通过传入的面边界字符串，生成对应的面对象
      public Polygon getPolygon(String geometry) throws ParseException{
          WKTReader reader = new WKTReader( geometryFactory );
          Polygon polygon = (Polygon) reader .read(geometry );
           return polygon ;
     }
}

```
- 使用示例
```java
GeometyMethod gm = new GeometyMethod();
//边界坐标 首尾点需一样，构成封闭面 且格式如下
String borderString = POLYGON((116.22 39.90,中间的一堆点,116.22 39.90));
Polygon pol = gm .getPolygon( borderString);
Boolean bl = gm .withinGeo( lon, lat , pol );

```
