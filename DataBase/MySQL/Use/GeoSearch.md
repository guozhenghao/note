# MySQL的空间查询
## 前期准备
### 数据格式
- Point
    - 字段类型: `point`
    - 样例: `Point(1 1)`
- MultiPoint
    - 字段类型: `multipoint`
    - 样例: `MULTIPOINT (1 1,2 2,3 3)`
- LineString
    - 字段类型: `linestring`
    - 样例: `LINESTRING(1 1,2 2,3 3)`
- MultiLineString
    - 字段类型: `multilinestring`
    - 样例: `MULTILINESTRING ((1 1,2 2,3 3),(2 2,2 3,2 4))`
- Polygon
    - 字段类型: `polygon`
    - 样例: `POLYGON((1 1,1 2,2 2,2 1,1 1))`
        - ***注意:首末点要相同，用于连接成一个闭合的面***
- MultiPolygon
    - 字段类型: `multipolygon`
    - 样例: `MULTIPOLYGON (((1 1,1 2,2 2,2 1,1 1),(2 2,2 3,3 2,2 2)))`

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
        - MBRCovered(A,B) --> A覆盖B
    - 相交相关
        - MBRDisjoint(A,B) --> A、B不相交
        - MBRIntersects(A,B) --> A、B相交
    - 接触
        - MBRTouches(A,B) --> A、B接触，接触的概念类似于相切
    - 重叠
        - MBROverlaps(A,B) --> A、B重叠
    - 相同
        - MBREquals(A,B) --> A、B相同
- ETC
    - ST_AsText(字段名) --> 以文本形式返回
    - ST_AsBinary(字段名) --> 以二进制形式返回
    - ST_X/ST_Y(字段名) --> 对于点，可以单独获取该字段的x或y

*包含、覆盖、重叠三个方法不清楚具体的区别*

*注意:每个方法前的MBR可要可不要，在mysql5.7.6之后，不带MBR的方法将逐渐被去除*

查询样例:
- 查询在指定面中的点
    ```sql
    select * from test where MBRContains(ST_GeomFromText('Polygon((0 0,0 5,5 5,5 0,0 0))'),point)
    ```
- 查询所有point点的x坐标
    ```sql
    SELECT ST_X(point) FROM test
    ```