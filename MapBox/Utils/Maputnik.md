# maputnik

负责接入发布的mbtiles数据，并进行可视化风格配置。配置完成后，可以导出样式文件。

[*安装*](https://github.com/maputnik/editor)

[*详细教程*](https://github.com/maputnik/editor/wiki)

主页地址：http://ip/maputnik

- 操作流程 

### 1.添加数据源

在最上工具栏点击Sources,输入数据源参数

参数 | 说明
----|------
Source ID | 自定义数据源名称
Source Type | Vector(XYZ URLs)
1st Tile URL | 格式：http://ip/data/mbtiles文件名/{z}/{x}/{y}.pbf

### 2.添加图层

添加完数据源后，在左侧的面板上方点击add layer，然后按照自己的需求配置参数即可

### 3.风格发布

首先导出风格文件，在最上方工具栏点击Export,下载即可。

下载后修改风格文件，将不是自己配置的多余内容删除,不删除无法正常发布显示。

**注意**

如果sprite和font数据发布到线上，则填写线上地址，如果是服务器本地，则写相对于config.json的相对路径