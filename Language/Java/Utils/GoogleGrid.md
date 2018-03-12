# 谷歌网格与经纬度坐标的相互转换

- 经纬度转谷歌网格编号
```java
public String transxy(double lng, double lat,int zoom){
    int[] gridxy = new int[2];
    double pow= Math.pow(2,zoom);
    gridxy[0]=(int) ((180 + lng) * pow) / 360;
    double phi = Math.PI * lat / 180;
    double res = 0.5 * Math.log((1 + Math.sin(phi)) / (1 - Math.sin(phi)));
    gridxy[1]= (int)(((1 - res / Math.PI) / 2) * pow);
    return gridxy[0]+"-"+gridxy[1];
}
```

- 谷歌网格编号转经纬度坐标
```java
public double[] toLonLat(String lonLat,int zoom){
    double lon = Double.parseDouble(lonLat.split("-")[0]);
    double lat = Double.parseDouble(lonLat.split("-")[1]);
    double lon_min = lon / Math.pow(2,zoom) * 360 - 180;
    double lon_max = (lon + 1) / Math.pow(2,zoom) * 360 - 180;
    double lat_min = Math.atan(Math.sinh(Math.PI * (1 - ((lat - 1) / Math.pow(2,zoom - 1))))) * 180 / Math.PI;
    double lat_max = Math.atan(Math.sinh(Math.PI * (1 - (lat / Math.pow(2,zoom - 1))))) * 180 / Math.PI;
    double[] result = {(lon_min + lon_max)/2,(lat_min + lat_max) /2};
    return result;
}
```

