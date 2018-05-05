# Scrapy爬虫框架
-->[自己的demo](https://github.com/GuoZhenghao/scrapy-spider)<--

## 环境安装
- windows
    - 安装win32
        - -->[下载地址](https://sourceforge.net/projects/pywin32/files/pywin32/)<--
- linux
    - 需要安装如下依赖
        - ubuntu
            - python-dev
            - libevent-dev
        - centos
            - python-devel 
            - openssl-devel 
            - libxslt-devel 
            - libxml2-devel

    `pip install scrapy`
## 调试shell
```python
from scrapy.shell import inspect_response
inspect_response(response, self)
```
相当于断点，运行到这里后可以通过xpath等，取信息查看正确性