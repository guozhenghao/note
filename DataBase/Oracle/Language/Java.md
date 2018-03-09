# Java使用oracle
## maven依赖
按照版本自行下载，在maven仓库自行查询
```xml
<dependency>
    <groupId>com.oracle</groupId>
    <artifactId>ojdbc6</artifactId>
    <version>11.2.0.3</version>
</dependency>
```
## 使用
- 链接demo
```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class OracleConn {
	
	private String ip;
	private String port;
	private String databaseName;
    private String user;
    private String password;
	
	public OracleConn(String ip, String port, String databaseName) {
		super();
		this.ip = ip;
		this.port = port;
		this.databaseName = databaseName;
        this.user = user;
		this.password = password;
	}

	/**
	 * 获取oracle连接
	 *  
	 * @return Connection
	 */
	public Connection getConn(){
	    Connection conn = null;
	    try{
	        Class.forName("oracle.jdbc.driver.OracleDriver");
	        String url = "jdbc:oracle:thin:@" + ip + ":" + port + ":" + databaseName;
	        conn = DriverManager.getConnection(url, user, password);	        
	    }
	    catch (Exception e){
	        e.printStackTrace();
	    }
		return conn; 
	}
	
	public static void main(String args[]){
		OracleConn oc = new OracleConn("192.168.1.1","1521","orcl","user","pass");
		Connection conn = oc.getConn();
        String sql = "select * from POSITION where rownum < 10000000";// 预编译语句，“？”代表参数
		PreparedStatement pre;
		try {
			pre = conn.prepareStatement(sql);
			//pre.setString(1, "CLERK");// 设置参数，前面的1表示参数的索引，而不是表中列名的索引
			ResultSet result = pre.executeQuery();// 执行查询，注意括号中不需要再加参数
	        while (result.next())
	            // 当结果集不为空时
	            System.out.println("**********************" + result.getString(1) + "***************************");
	        conn.close();
	        
		} catch (SQLException e) {
			e.printStackTrace();
		}
        
	}
}

```
