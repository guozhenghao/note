# MySQL的空间查询

### MySQL遵守OGC的OpenGIS Geometry Model，支持以下空间数据对象

- Geometry (non-instantiable)
    - Point (instantiable)
    - Curve (non-instantiable)
        - LineString (instantiable)
            - Line
            - LinearRing
    - Surface (non-instantiable)
        - Polygon (instantiable)
    - GeometryCollection (instantiable)
        - MultiPoint (instantiable)
        - MultiCurve (non-instantiable)
            - MultiLineString (instantiable)
        - MultiSurface (non-instantiable)
            - MultiPolygon (instantiable)

一个样例，使用后再写笔记
````
SELECT id,longitude,latitude FROM zl_vehicles WHERE st_distance(point(longitude,latitude), GeomFromText('POINT(116.159166666667	40.1762333333333)'))* 111195 < 500;
````