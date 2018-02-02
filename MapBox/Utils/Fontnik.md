# MapBox本地化样式字体制作
### 安装
软件需要node环境，版本要求为：v0.10.x, v0.12.x, v4.x or v6.x
- 所需安装软件
    - [node-fontnik](https://github.com/mapbox/node-fontnik)
        - npm install -g fontnik
    - [genfontgl](https://github.com/sabas/genfontgl)
        - npm install -g genfontgl
- 所需资源
    - [字体库](https://github.com/mapbox/mapbox-studio-default-fonts)
        - 可自己找字体
### 使用
- genfontgl [字体文件otf或ttf] [输出目录]
    - 样例

        `genfontgl Ubuntu-BoldItalic.ttf /root/map/test/`

因为字体文件较多，可在https://www.mapbox.com/install/studio/中创建一张地图后，在字体列表预览，然后选择需要的字体。