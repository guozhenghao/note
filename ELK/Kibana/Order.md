# 基础命令
打开kibana，在左侧侧边栏中点击"dev tools"
- 查看健康状态
    - console:

        `GET /_cat/health?v`
    - 请求:

        `curl -XGET 'localhost:9200/_cat/health?v&pretty'`
        
        后面操作注意get和put等修改请求就行，只写console中的用法了
- http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html 留坑