# Java操作excel
## 前期准备
- 导入jar包

    目前看到两个相关支持，分别是poi和jxl

    本次使用的是poi

    ```xml
    <!-- https://mvnrepository.com/artifact/org.apache.poi/poi -->
    <dependency>
        <groupId>org.apache.poi</groupId>
        <artifactId>poi</artifactId>
        <version>3.17</version>
    </dependency>
    ```
## 使用
- 生成xls文件内容
```java
//创建HSSFWorkbook对象(excel的文档对象)
HSSFWorkbook wb = new HSSFWorkbook();
//建立新的sheet对象（excel的表单）
HSSFSheet sheet=wb.createSheet("采集需求管理表");
//在sheet里创建第一行，参数为行索引(excel的行)，可以是0～65535之间的任何一个
HSSFRow row1=sheet.createRow(0);
//创建单元格（excel的单元格，参数为列索引，可以是0～255之间的任何一个
HSSFCell cell=row1.createCell(0);
//设置单元格内容
cell.setCellValue("采集需求管理表");
//设置单元格样式
HSSFCellStyle cellStyleTatle = wb.createCellStyle();
cellStyleTatle.setAlignment(XSSFCellStyle.ALIGN_CENTER); // 左右居中
cellStyleTatle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);// 垂直居中
//设置字体
HSSFFont font = wb.createFont();
font.setFontName("宋体");
font.setBoldweight(XSSFFont.BOLDWEIGHT_BOLD); // 粗体显示
font.setFontHeightInPoints((short) 14);// 设置字体大小
cellStyleTatle.setFont(font);
//将样式添加
cell.setCellStyle(cellStyleTatle);
//合并单元格CellRangeAddress构造参数依次表示起始行，截至行，起始列， 截至列
sheet.addMergedRegion(new CellRangeAddress(0,0,0,12));
//在sheet里创建第二行
HSSFRow row2=sheet.createRow(1);
//创建单元格并设置单元格内容
row2.createCell(0).setCellValue("需求编号");
row2.createCell(1).setCellValue("需求名称");
row2.createCell(2).setCellValue("城市代码");
//后面循环添加什么的就行了
HSSFRow row=sheet.createRow(2);
row.createCell(0).setCellValue("xxxxxxx");
//如果添加样式，上面要单独创建一个cell，然后添加样式，然后row添加进去
//可以设置row的样式
//具体使用，网上去查
```

- 将创建内容写入实体文件
```java
//接上面那一坨
File xlsFile = new File("test.xls");
FileOutputStream xlsStream = new FileOutputStream(xlsFile);
wb.write(xlsStream);
//服务中也可以直接将写好的文件瞎子啊，wb.write(服务中的流，可以具体参考笔记java中的文件下载);
```