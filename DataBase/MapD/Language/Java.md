# Java操作MapD
### 前期准备
- 导入包

1. mapdjdbc-1.0-SNAPSHOT-jar-with-dependencies.jar

- 创建一个数据库工具类，用于连接数据库等操作

e.g.

```java
import java.sql.Connection;
import java.sql.SQLException;

import org.apache.commons.dbcp2.BasicDataSource;


public enum MapdUtil {

	mapdutil;

	private BasicDataSource basicDataSource = null;

	static{
		mapdutil.basicDataSource = new BasicDataSource();
		mapdutil.basicDataSource.setDriverClassName("com.mapd.jdbc.MapDDriver");
		mapdutil.basicDataSource.setUrl("jdbc:mapd:123.123.123.123:123:test");
		mapdutil.basicDataSource.setUsername("用户名");
		mapdutil.basicDataSource.setPassword("密码");
	}

	public Connection getConnection() throws SQLException{
		Connection connection = mapdutil.basicDataSource.getConnection();
		return connection;	
	}

	public void cloneConn(Connection conn) throws SQLException{
		if(conn!=null){
			conn.close();
		}
	}
}
```
### 使用
```java
PreparedStatement ps = null;
Connection conn = null ;

conn = MapdUtil. mapdutil.getConnection();
String sql = "SELECT From_grid_id, Lon, Lat, people_num from beijing_od where To_grid_id=? ";
ps = conn.prepareStatement( sql);
ps.setInt(1, Integer. parseInt( id));
ResultSet resultSet = ps .executeQuery();
//处理..
//断开连接
if (ps != null){ ps.close();}
if (conn != null){MapdUtil. mapdutil.cloneConn( conn);}

```