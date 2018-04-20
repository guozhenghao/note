# MySQL的空间查询
## 前期准备
### 数据格式
- Point
    - 字段类型: `point`
    - 样例: `Point(1 1)`
- MultiPoint
    - 字段类型: `multipoint`
    - 样例: `MULTIPOINT (1 1,2 2,3 3)`
        - 里面是多个point
- LineString
    - 字段类型: `linestring`
    - 样例: `LINESTRING(1 1,2 2,3 3)`
- MultiLineString
    - 字段类型: `multilinestring`
    - 样例: `MULTILINESTRING ((1 1,2 2,3 3),(2 2,2 3,2 4))`
- Polygon
    - 字段类型: `polygon`
    - 样例: 
        - 单面 `POLYGON((1 1,1 2,2 2,2 1,1 1))`
        - 镂空面 `POLYGON((1 1,,,),(2 2,,,),(3 3,,,))`
            - 会从第一个面中，去除后面的面
            - 使用ST_Area方法计算面积时，只会算出几个面的面积，用第一个面减，如果后面的面有超过了第一个面范围的部分，会将超出的数值一并减掉。单纯的面积减面积。
    - ***注意:首末点要相同，用于连接成一个闭合的面***
- MultiPolygon
    - 字段类型: `multipolygon`
    - 样例: `MULTIPOLYGON(((1 1,1 2,2 2,2 1,1 1)),((2 2,2 3,3 2,2 2)))`
        - 里面是多个polygon


### 数据插入
```sql
INSERT into 表明(字段名) VALUES(ST_GeomFromText('上方列举的数据格式'))
```
以插入一个point为例:
```
INSERT into test(point) VALUES(ST_GeomFromText('Point(5 5)'))
```

*插入时使用ST_GeomFromText，也可使用GeomFromText，区别暂时没有研究。该方法是返回源字符串的相应类型的几何值*

### 创建索引
先空着，以后再写-->[点击进入官方解释](https://dev.mysql.com/doc/refman/5.7/en/creating-spatial-indexes.html)<--

## 使用
- 空间查询相关方法
    - 包含相关
        - MBRContains(A,B) --> A包含B
        - MBRWithin(A,B) --> A在B中
            - *注意:包含关系中，所要验证的集合必须全部包含在指定的集合中。如果只有部分在其中，应该使用相交*
    - 覆盖相关
        - MBRCoveredBy(A,B) --> A被B覆盖
        - MBRCovers(A,B) --> A覆盖B
    - 相交相关
        - MBRDisjoint(A,B) --> A、B不相交
        - MBRIntersects(A,B) --> A、B相交
    - 接触
        - MBRTouches(A,B) --> A、B接触，接触的概念类似于相切
    - 重叠
        - MBROverlaps(A,B) --> A、B重叠
    - 相同
        - MBREquals(A,B) --> A、B相同
- 空间数据相关方法
    - 点独有
        - 开始、结束点
            - ST_StartPoint(A)
            - ST_EndPoint(A)
        - 获取x或y
            - ST_X(A)
            - ST_Y(A)
        - 凸包
            - ST_ConvexHull(A) --> 多点A的凸包面
        - 返回矩形
            - ST_MakeEnvelope(A,B) --> A、B为对角点
    - 线独有
        - 线是否闭合
            - ST_IsClosed(A)
        - 线中点数量
            - ST_NumPoints
        - 线中第n个点
            - ST_PointN(A,n)
        - 线长度
            - ST_Length(A)
        - 生成矩形
            - ST_Envelope(A) --> A只有两个点，且不是水平或竖直线
    - 面独有
        - 面积
            - ST_Area(A)
        - 面的内外边界
            - ST_ExteriorRing(A) --> 获取A面外环边界，返回值为LineString
            - ST_InteriorRingN(A,num) --> 获取A面中第num个内环边界，返回值为LineString。num从1开始。
                - ST_NumInteriorRings(A) --> 获取A面内环数量(5.7.8后添加ST_NumInteriorRing，效果一样)
    - 部分geo对象可用
        - 集合
            - 交集
                - ST_Intersection(A,B)
            - 异或
                - ST_SymDifference(A,B) --> A、B中独有的
            - 并集
                - ST_Union(A,B)
        - 质心
            - ST_Centroid(A)
        - 距离
            - ST_Distance(A,B) --> A和B距离
            - ST_Distance_Sphere(A,B) --> A和B的球面距离
        - 不同
            - ST_Difference(A,B) --> 返回A中有B中没有的
        - 简化
            - ST_Simplify(A,mix_distance) --> 将A抽稀，简化A中两点距离小于max的值(用起来有点迷。。待研究)   
        - 缓冲区
            - ST_Buffer(A,length) --> 通过A几何体，生成他周边范围为length距离的面
                - 5.7.7后可以添加策略影响缓冲区的计算
                    - point策略
                        - point_circle
                        - point_square
                    - join策略
                        - join_round
                        - join_miter
                    - end策略
                        - end_round
                        - end_flat
                - ***这块贼尼玛玄学，等我再思考一波***
                    - http://www.fooplot.com/
                    - https://dev.mysql.com/doc/refman/5.7/en/spatial-operator-functions.html#function_st-buffer-strategy
    - 上部分中的空间查询相关方法的判断方法，返回值是是否
        - 相交
            - ST_Intersects(A,B) --> A和B是否相交
            - ST_Crosses(A,B) --> A和B是否相交(相交部分不等于A或B)
            - ST_Disjoint(A,B) --> A和B是否不相交
        - 重叠
            - ST_Overlaps(A,B)
        - 接触
            - ST_Touches(A,B)
        - 包含
            - ST_Contains(A,B) --> A是否包含B
            - ST_Within(A,B) --> A是否在B中
- ETC
    - 验证数据是否合法
        - ST_IsValid(A)
        - ST_Validate(A)
    - geo对象返回格式
        - ST_AsText(字段名) --> 以文本形式返回
        - ST_AsBinary(字段名) --> 以二进制形式返回

*包含、覆盖、重叠三个方法不清楚具体的区别*

*注意:每个方法前的MBR、ST可要可不要，在mysql5.7.6之后，不带MBR、ST的方法开始弃用*

查询样例:
- 查询在指定面中的点
    ```sql
    select * from test where MBRContains(ST_GeomFromText('Polygon((0 0,0 5,5 5,5 0,0 0))'),point)
    ```
- 查询所有point点的x坐标
    ```sql
    SELECT ST_X(point) FROM test
    ```