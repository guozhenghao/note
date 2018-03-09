# Maven项目打包
### Eclipse
- 1

    首先在项目根目录中创建lib文件夹

    右键该项目--> run as --> maven build 在goals中填写如下内容

    `--dependency:copy-dependencies -DoutputDirectory=lib`

    用途是将所有的依赖包导入到lib文件夹中

- 2

    在项目根目录中创建MANIFEST.MF文件，复制如下内容

    ```yaml
    Manifest-Version: 1.0
    Premain-Class: com.geekcap.openapm.jvm.agent.Agent
    Can-Redefine-Classes: true
    Can-Retransform-Classes: true
    Can-Set-Native-Method-Prefix: true

    ```

    重点：文件最后一行必须空出来，每一行结尾不能有空格，每一行最多70个字节，换行前面必须要有空格

- 3

    编辑pom文件，在文件最后添加如下代码

    ```xml
    <build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-jar-plugin</artifactId>
				<configuration>
					<archive>
						<manifestFile>
							MANIFEST.MF （文件位置）
						</manifestFile>
						<manifest>
							<addClasspath>true</addClasspath>
							<classpathPrefix>lib/</classpathPrefix> (本地包位置)
							<mainClass>
								com.dataojo.ship_producer.producer.ShipProducer （main方法位置）
							</mainClass>
						</manifest>
					</archive>
				</configuration>
			</plugin>
		</plugins>
	</build>
    ```
    以防万一，可以在完成这部后，执行1中的操作
- 4

    执行maven打包命令，右键项目 --> run as -> maven build 在goals中填写如内容
    
    `--package`

- 5

    打包的文件在target文件夹中，创建一个文件夹，将此jar包、lib文件夹放入其中，如果程序还需其他文件支持，按照程序路径，在该文件夹中创建即可。

    完成后java -jar xxx.jar即可

### IDEA

只能，勾几个选项就可以了。。网上查