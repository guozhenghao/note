# Python操作MongoDB
### **前期准备**
- 需要安装pymongo *(没有pip先安装pip)*

    `pip install pymongo`
### **使用**
```python
# encoding:utf-8
from pymongo import MongoClient
import json
from urllib import quote_plus

uri = "mongodb://%s:%s@%s" % (quote_plus('用户名'), quote_plus('密码'), '123.123.123.123:27017')
dbClient = MongoClient(uri)
dbname = "库名"
db = dbClient[dbname]
coll_test = db["表明"]

a = coll_haidian_od.find()

# 取出后的数据是游标，通过next可以取出每一项，每项都是一个json类型

```