# python使用linux命令

- 得到运行时的输入值
```python
import sys

pyName = sys.argv[0]
参数1 = sys.argv[1]
参数2 = sys.argv[2]
```
- 使用linux命令
```python
import os

os.system("mkdir -p /asdfasdf/asdf")
```
- 是否存在文件或文件夹
```python
import os

os.path.exists("path")
```

### 注意，每次使用os.system使用linux命令时，系统都会新开一个进程执行，没有环境变量，所以需要写全命令路径，通过which is xxxx查询