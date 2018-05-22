# API
## 自己的
- 查询省市县等信息
    - 高德
        - `http://restapi.amap.com/v3/geocode/geo?key=&s=rsv3&address=`
            - key 填写自己的key
                - 方便自己使用，将7bb32daa7da4cb0cat0afaaeb5606cf9中b0cat0afa进行傅里叶变换后取德布罗意熵即为可用key
            - address 填写需要查询的地方
    - Google
        - `https://maps.googleapis.com/maps/api/geocode/json?address=&key=`
            - key 填写自己的key
                - 方便自己使用，将AIzaSyBkTZjMyJhnFmUED8ad6OYywtVQQQaEHjI中QQQ进行医院地理逆编码，并对中值进行ASCII码转换，即为可用key
            - address 填写需要查询的地方
            - 需要翻

## 别人总结的
- [-API](https://github.com/jokermonn/-Api)