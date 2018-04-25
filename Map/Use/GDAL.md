# GDAL
目前只是用了通过gdal将shp文件转换为geojson文件。

使用语言为Java
## 前期准备
需要gdal的jar包和dll
- 下载gdal的相关文件

    登录网址：http://www.gisinternals.com/sdk.php

    下载对应的所需版本即可(我使用的是release-1900-x64-dev)
- 导入jar包

    将下载的文件解压，进入到同名文件夹下的bin->gdal->java中，将jar包导入到项目

- 导入dll文件

    gdal需要相关环境

    - 方法一：

        将解压目录的\release-1900-x64-dev\release-1800-x64\bin\gdal\java和\release-1900-x64-dev\release-1800-x64\bin添加到环境变量path中。
    
    - 方法二：

        将\release-1900-x64-dev\release-1800-x64\bin\gdal\java和\release-1900-x64-dev\release-1800-x64\bin中的dll文件全部拷贝到项目的根路径下。

    **如果是linux运行，需要安装linux版本的gdal，然后将linux的os文件按照上述方法进行拷贝**
## 使用
```java
import org.gdal.*;
import org.gdal.ogr.DataSource;
import org.gdal.ogr.Driver;
import org.gdal.ogr.ogr;
import org.gdal.gdal.*;  
public class Test {
    public static void main(String[] args) {
         // 注册所有的驱动  
        ogr.RegisterAll();
        // 为了支持中文路径，请添加下面这句代码  
        gdal.SetConfigOption("GDAL_FILENAME_IS_UTF8","YES");  
        // 为了使属性表字段支持中文，请添加下面这句  
        gdal.SetConfigOption("SHAPE_ENCODING","");  
          
        String strVectorFile = "shp文件路径，如C:\\xx\\xx\\test.shp";  
        //打开数据  
        DataSource ds = ogr.Open(strVectorFile,0);  
        if (ds == null)  
        {  
            System.out.println("打开文件失败！" );  
            return;  
        }  
        System.out.println("打开文件成功！" );  
        Driver dv = ogr.GetDriverByName("GeoJSON");  
        if (dv == null)  
        {  
            System.out.println("打开驱动失败！" );  
            return;  
        }  
        System.out.println("打开驱动成功！" );  
        dv.CopyDataSource(ds, "转换后输出的geojson文件路径，如C:\\xx\\xx\\test.geojson");  
        System.out.println("转换成功！" );  
    }
}

```