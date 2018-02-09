# 基础命令
打开kibana，在左侧侧边栏中点击"dev tools"
- 查看所有index
    - console:

        `GET /_cat/health?v`
    - 请求:

        `curl -XGET 'localhost:9200/_cat/health?v&pretty'`
        
- index相关
    - 新建
        
        `PUT /index_name`
    - 删除

        `DELETE /index_name`

- document相关
    - 插入/更新

        ````
        PUT /index_name/type_name/id
        {
            "name":"ZhangSan"
        }
        ````
        
        id不指定会随机生成 如：AV3qGfrC6jMbsbXb6k1p

    - 查询
        - 查询某一条
            
            `GET /index_name/type_name/id`
        - 查询所有

            `GET /index_name/type_name/_search`
    