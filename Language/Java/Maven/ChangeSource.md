# Maven修改下载源
- 将代码复制到maven的setting文件中
````
<mirror>  
    <id>alimaven</id>  
    <mirrorOf>central</mirrorOf>    
    <name>aliyun maven</name>  
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url> 
</mirror>  
````