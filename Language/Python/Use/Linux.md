# python使用linux命令

- 得到运行时的输入值
````
import sys

pyName = sys.argv[0]
参数1 = sys.argv[1]
参数2 = sys.argv[2]
````
- 使用linux命令
````
import os

os.system("mkdir -p /asdfasdf/asdf")
````
- 是否存在文件或文件夹
````
import os

os.path.exists("path")
````
