# 网络请求

## 需要header、cookie、gzip

```python
url = r'https://xxxxxxxxxxxxxx'
# 需要什么添什么
header = {
        "Accept-Encoding":"gzip, deflate, br",
        "Connection":"keep-alive",
        "Host":"...",
        "Cookie":"..."
        }
request = urllib2.Request(url,headers=header)
response = urllib2.urlopen(request)
content = ""
# gzip解码
encoding = response.headers["content-encoding"]
if encoding == 'gzip':
    buf = StringIO(response.read())
    f = gzip.GzipFile(fileobj=buf)
    content = f.read()
else:
    content = response.read()
jsonFile = json.loads(content)
```

## 不需要
```python
response = urllib2.urlopen(r'http://xxxxx')
jsonInfo = response.read()
jsonFile = json.loads(jsonInfo)
```