# 读写文件
## Web项目
### web项目部署的时候，只会保留webapp中的内容，所需要将上传的文件放在该文件目录下。

- 示例 *（文件放在了webapp下的web-inf下，使用File.separator是为了适配linux和windows不同的文件目录符号“//”或“\”）*

````
InputStream inputStream = this .getServletContext().getResourceAsStream( "WEB-INF"+File. separator +"文件名" );
BufferedReader bf = new BufferedReader(new InputStreamReader(inputStream , "utf-8"));
String temp = "" ;
while ((temp = bf .readLine()) != null) {
}

````

## 普通Java项目
### 读取服务器上的位置或者本地位置
- 示例

````
//服务器
String filePath = String. valueOf("/usr/xskj/taxi/123.csv" );
//本地
String filePath = String. valueOf("C://123.csv" );
BufferedReader br = new BufferedReader( new InputStreamReader(new FileInputStream( filePath), "UTF-8" ));
String json = null ;
while ((json = br .readLine()) != null) {
}

````

### 写文件
- 示例
````
String path= "C://test.txt";
File file= new File(path );
String str = "12345678913";
FileOutputStream out;
out = new FileOutputStream(file , true);
StringBuffer sb= new StringBuffer();
sb.append( str);
out .write(sb .toString().getBytes( "utf-8")); //注意需要转换对应的字符集
out.close();

````