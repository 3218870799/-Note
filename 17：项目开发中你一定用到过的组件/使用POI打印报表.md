公司很多报表打印，将报表打印部分做一个小小梳理，以后可复用

## 一：POI简介

POI是Java编写的可以提供对office的操作，是“Poor Obfuscation Implementation”的首字母缩写，意为“简洁版的模糊实现”。

依赖

```xml
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>3.9</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>3.9</version>
</dependency>
```

### 1：包结构

HSSF提供读写Microsoft Excel XLS格式档案的功能。

XSSF提供读写Microsoft Excel OOXML XLSX格式档案的功能。

HWPF提供读写Microsoft Word DOC格式档案的功能。

HSLF提供读写Microsoft PowerPoint格式档案的功能。

HDGF提供读Microsoft Visio格式档案的功能。

HPBF提供读Microsoft Publisher格式档案的功能。

HSMF提供读Microsoft Outlook格式档案的功能。

### 2：常用类与方法说明

 **工作簿Workbook**

其下有两个实现类：

- HSSFWorkbook : 有读取.xls 格式和写入Microsoft Excel文件的方法。它与微软Office97-2003版本兼容
- XSSFWorkbook : 有读写Microsoft Excel和OpenOffice的XML文件的格式.xls或.xlsx的方法。它与MS-Office版本2007或更高版本兼容

**标签页Sheet**

**HSSFSheet** 和 **XSSFSheet** 都是Sheet接口的实现类，Sheet可以使用Workbook的两个方法获得：

**行 Row**

**单元格 Cell**





## 二：程序书写打印报表

打印报表只需两步：一获取要打印的数据，二将数据写入到excel中

以打印公司联系人通讯录为例：

### 1：打印公司通信录（主要步骤）

```java
  /*
    * 操作步骤：

     * 1、获取数据

      * 2、将数据写入到excel文件中

  */

public void print() throws FileNotFoundException, IOException{
              //设置查询条件
              Factory factory = new Factory();
              //只查询没禁用的
              factory.setState(1);
    
              //第一步：获取数据
              List<Factory> dataList = factoryService.find(factory);
              String[] title = new String[]{"厂家全称","缩写","联系人","电话","手机","传真","备注"};          

              //第二步
              //创建工作簿，使用HSSF
              Workbook wb = new HSSFWorkbook();
              //创建工作表
              Sheet sheet = wb.createSheet();
              int rowNo = 0; //行号，以便于复用
              int colNo = 0; //列号
              Row nRow = null;
              Cell nCell = null;
              sheet.setColumnWidth(0, 30*256);                                //设置列宽
              //创建行对象，起始行为rowNo
              nRow = sheet.createRow(rowNo);
              nRow.setHeightInPoints(40);
　　　　//合并单元格，新对象，不会覆盖合并的那些单元格，只是遮住，从rowNo行到rowNo行，从第0列到第六列 进行合并
              sheet.addMergedRegion(new CellRangeAddress(rowNo, rowNo, 0, 6));                    
              rowNo++;        
              //创建单元格（列）依附在行上的
              nCell = nRow.createCell(0);
              //设置单元格内容
              nCell.setCellValue("生产厂家通讯录");
              //设置大标题样式
              nCell.setCellStyle(this.bigTilteStyle(wb));        
              //写标题
              nRow = sheet.createRow(rowNo++);
              nRow.setHeightInPoints(28);//设置行高                    
              for(int i=0;i<title.length;i++){
                     nCell = nRow.createCell(i);
                     nCell.setCellValue(title[i]);
                     nCell.setCellStyle(this.titleStyle(wb));         //绑定样式
              }
              //写数据
              for(int j=0;j<dataList.size();j++){
                     colNo = 0;                                                      //初始化
                     Factory f = dataList.get(j);                                //获取到每条厂家记录           
                     nRow = sheet.createRow(rowNo++);
                     nRow.setHeightInPoints(21);                  
                     nCell = nRow.createCell(colNo++);
                     nCell.setCellValue(f.getFullName());
                     nCell.setCellStyle(this.textStyle(wb));
                     nCell = nRow.createCell(colNo++);
                     nCell.setCellValue(f.getFactoryName());
                     nCell.setCellStyle(this.textStyle(wb));                   
                     nCell = nRow.createCell(colNo++);
                     nCell.setCellValue(f.getContractor());
                     nCell.setCellStyle(this.textStyle(wb));
                     nCell = nRow.createCell(colNo++);
                     nCell.setCellValue(f.getPhone());
                     nCell.setCellStyle(this.textStyle(wb));
                     nCell = nRow.createCell(colNo++);
                     nCell.setCellValue(f.getMobile());
                     nCell.setCellStyle(this.textStyle(wb));
                     nCell = nRow.createCell(colNo++);
                     nCell.setCellValue(f.getFax());
                     nCell.setCellStyle(this.textStyle(wb));
                     nCell = nRow.createCell(colNo++);
                     nCell.setCellValue(f.getCnote());
                     nCell.setCellStyle(this.textStyle(wb));
              }

              //文件输出流
              FileOutputStream os = new FileOutputStream("c:\\factory.xls");//输出流
              wb.write(os);//写入到文件中
              os.flush();//清空缓存
              os.close();//关闭
       }
```



   

### 2：设置单元格样式

```java
//大标题样式

       private CellStyle bigTilteStyle(Workbook wb){
              //创建一个单元格样式对象
              CellStyle curStyle = wb.createCellStyle();
              curStyle.setAlignment(CellStyle.ALIGN_CENTER);//横向居中
              curStyle.setVerticalAlignment(CellStyle.VERTICAL_CENTER); //纵向居中
              Font curFont = wb.createFont();                                                                				//创建字体对象
              curFont.setFontName("华文隶书");                                                                   			//设置字体
              curFont.setFontHeightInPoints((short)30);  //设置字体大小
              curStyle.setFont(curFont);//将字体对象绑定到样式对象上 
              return curStyle;
       } 
       //标题样式
       private CellStyle titleStyle(Workbook wb){
              //创建一个单元格样式对象
              CellStyle curStyle = wb.createCellStyle();
              curStyle.setAlignment(CellStyle.ALIGN_CENTER);                                       //横向居中
              curStyle.setVerticalAlignment(CellStyle.VERTICAL_CENTER);                //纵向居中
              Font curFont = wb.createFont();                                                                //创建字体对象
              curFont.setFontName("微软雅黑");                                                                   //设置字体
              curFont.setFontHeightInPoints((short)12);                                           //设置字体大小       
              curStyle.setFont(curFont);                                                                          //将字体对象绑定到样式对象上
             //画线
              curStyle.setBorderTop(CellStyle.BORDER_THIN);                                        //细实线
              curStyle.setBorderBottom(CellStyle.BORDER_THIN);
              curStyle.setBorderLeft(CellStyle.BORDER_THIN);
              curStyle.setBorderRight(CellStyle.BORDER_THIN);
              return curStyle;
       }

       //文本样式

       private CellStyle textStyle(Workbook wb){
              //创建单元格样式
              CellStyle xStyle = wb.createCellStyle();
              //创建字体样式
              Font xFont = wb.createFont();
              xStyle.setFont(xFont);
              //垂直居中
              xStyle.setVerticalAlignment(CellStyle.VERTICAL_CENTER);                   //纵向居中
              //画线
              xStyle.setBorderTop(CellStyle.BORDER_THIN);                                    //细实
             xStyle.setBorderBottom(CellStyle.BORDER_THIN);
              xStyle.setBorderLeft(CellStyle.BORDER_THIN);
              xStyle.setBorderRight(CellStyle.BORDER_THIN);
              return xStyle;
       }
```

POI处理列宽时有BUG，列宽精度不够。

### 3：产生临时的excel存放在临时文件夹下

```java
         String path = request.getSession().getServletContext().getRealPath("/");//虚拟路径对应的真实物理路径
              path += "/tmpfile"; //防止tomcat8不能直接获取.getRealPath("/tmpfile")会为null
              File file = new File(path);
              if(!file.exists()){
                     file.mkdirs();                        //创建多级目录
              }
```



### 4：文件重命名防止并发打印冲突

```java
      FileUtil fu = new FileUtil();
      String fileName = path + "/" + fu.newFile(path, "factory.xls");       //产生新的文件名，防止冲突
```





 

工具类方法

```java
/* 目录下已经有同名文件,则文件重命名,增加文件序号*/
       public String newFile(String sPath, String sFile){
             String newFileName = new String();
              String withoutExt = new String();
              File curFile = new File(sPath + "\\" + sFile);
              if (curFile.exists()) {
                     for(int counter = 1; curFile.exists(); counter++){
                            withoutExt = this.getNameWithoutExtension(curFile.getName());
                            if(withoutExt.endsWith(counter-1 + ")")){
                                   withoutExt = withoutExt.substring(0,withoutExt.indexOf("("));        //idea
                            }
                newFileName = withoutExt + "(" + counter + ")" + "." + getFileExt(curFile.getName());
                curFile = new File(sPath + "\\" + newFileName);
            }
              }else{
                     newFileName = curFile.getName();
              }
              return newFileName;
       }
```

### 5：用户下载打印的报表

用户下载有两种方法：

1） 有临时文件，给下载传下载文件，页面就可以下载

2） 直接写入到response中

　　　　//生成流对象

​       ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();        

​       wb.write(byteArrayOutputStream);

​       

​       DownloadUtil du = new DownloadUtil();

​       //du.prototypeDownload(file, returnName, response, delFlag)              //下载临时文件，下载后删除

​       du.download(byteArrayOutputStream, response, "货物订单.xls");    //弹出下载框，用户就可以直接下载

Download工具

```java
import java.io.ByteArrayOutputStream;

import java.io.File;

import java.io.FileInputStream;

import java.io.IOException;



import javax.servlet.ServletOutputStream;

import javax.servlet.http.HttpServletResponse;



public class DownloadUtil {



       /**

        * @param filePath 要下载的文件路径

        * @param returnName 返回的文件名

        * @param response HttpServletResponse

        * @param delFlag 是否删除文件

        */

       protected void download(String filePath,String returnName,HttpServletResponse response,boolean delFlag){

              this.prototypeDownload(new File(filePath), returnName, response, delFlag);

       }





       /**

        * @param file 要下载的文件

        * @param returnName 返回的文件名

        * @param response HttpServletResponse

        * @param delFlag 是否删除文件

        */

       protected void download(File file,String returnName,HttpServletResponse response,boolean delFlag){

              this.prototypeDownload(file, returnName, response, delFlag);

       }



       /**

        * @param file 要下载的文件

        * @param returnName 返回的文件名

        * @param response HttpServletResponse

        * @param delFlag 是否删除文件

        */

       public void prototypeDownload(File file,String returnName,HttpServletResponse response,boolean delFlag){

              // 下载文件

              FileInputStream inputStream = null;

              ServletOutputStream outputStream = null;

              try {

                     if(!file.exists()) return;

                     response.reset();

                     //设置响应类型    PDF文件为"application/pdf"，WORD文件为："application/msword"， EXCEL文件为："application/vnd.ms-excel"。

                     response.setContentType("application/octet-stream;charset=utf-8");



                     //设置响应的文件名称,并转换成中文编码

                     //returnName = URLEncoder.encode(returnName,"UTF-8");

                     returnName = response.encodeURL(new String(returnName.getBytes(),"iso8859-1"));    //保存的文件名,必须和页面编码一致,否则乱码



                     //attachment作为附件下载；inline客户端机器有安装匹配程序，则直接打开；注意改变配置，清除缓存，否则可能不能看到效果

                     response.addHeader("Content-Disposition",   "attachment;filename="+returnName);



                     //将文件读入响应流

                     inputStream = new FileInputStream(file);

                     outputStream = response.getOutputStream();

                     int length = 1024;

                     int readLength=0;

                     byte buf[] = new byte[1024];

                     readLength = inputStream.read(buf, 0, length);

                     while (readLength != -1) {

                            outputStream.write(buf, 0, readLength);

                            readLength = inputStream.read(buf, 0, length);

                     }

              } catch (Exception e) {

                     e.printStackTrace();

              } finally {

                     try {

                            outputStream.flush();

                     } catch (IOException e) {

                            e.printStackTrace();

                     }

                     try {

                            outputStream.close();

                     } catch (IOException e) {

                            e.printStackTrace();

                     }

                     try {

                            inputStream.close();

                     } catch (IOException e) {

                            e.printStackTrace();

                     }

                     //删除原文件



                     if(delFlag) {

                            file.delete();

                     }

              }

       }



       /**

        * @param byteArrayOutputStream 将文件内容写入ByteArrayOutputStream

        * @param response HttpServletResponse      写入response

        * @param returnName 返回的文件名

        */

       public void download(ByteArrayOutputStream byteArrayOutputStream, HttpServletResponse response, String returnName) throws IOException{

              response.setContentType("application/octet-stream;charset=utf-8");

              returnName = response.encodeURL(new String(returnName.getBytes(),"iso8859-1"));               //保存的文件名,必须和页面编码一致,否则乱码

              response.addHeader("Content-Disposition",   "attachment;filename=" + returnName);

              response.setContentLength(byteArrayOutputStream.size());



              ServletOutputStream outputstream = response.getOutputStream();    //取得输出流

              byteArrayOutputStream.writeTo(outputstream);                                 //写到输出流

              byteArrayOutputStream.close();                                                         //关闭

              outputstream.flush();                                                                         //刷数据

       }

}
```



## 三：导入Excel模板打印报表

### 1：生产厂家POI打印缺点：

1） 当静态文字变更时，需要修改代码

2） 样式代码繁多，不灵活，修改代码

3） 表格标题每页都出现

4） 打印加页脚，写入当前页总页数（报表引擎）

### 2：模板打印

**1：用excel 2003书写模板文件**

**2：代码打印：只需要三步，一读取数据，二读取模板样式，三根据模板样式写入数据**

```java
    @RequestMapping("/cargo/outproduct/outProductPrint.action")

​    public void print(String inputDate, HttpServletResponse response) throws FileNotFoundException, IOException, ParseException{

​       /*

​        \* 操作步骤：

​        \* 1、获取数据

​        \* 2、POI写数据到文件：读模板样式，设置模板样式

​        */

　　　　//1：获取数据

​       List<OutProduct> oList = contractService.findOutProduct(inputDate+"%");

​       //2：读取模板文件

​       Workbook wb = new HSSFWorkbook(new FileInputStream(new File("c:\\tFACTORY.xls")));             //打开模板文件

​       Sheet sheet = wb.getSheetAt(0);                  //打开第一个工作表

​       Row nRow = null;

​       Cell nCell = null;

​       int rowNo = 2;                                   //行号

​       int colNo = 1;                                //列号

​       

​       //处理标题

​       nRow = sheet.getRow(0);                           //获得行对象

​       nCell = nRow.getCell(1);                        //获得单元格对象

​       nCell.setCellValue(inputDate.replaceFirst("-0", "-").replaceFirst("-", "年")+"月份出货表");       //yyyy-MM 2010-08

​       

​       //获取模板文件中的样式

　　　　//获取第2行第1列的客户名称样式

​       nRow = sheet.getRow(2);

​       nCell = nRow.getCell(1);

​       CellStyle customNameStyle = nCell.getCellStyle();         //获取客户名称样式

​       //获取第2行第2列的合同编号样式

​       nRow = sheet.getRow(2);

​       nCell = nRow.getCell(2);

​       CellStyle contractNoStyle = nCell.getCellStyle();

​       //获取第2行第3列的产品编号样式

​       nRow = sheet.getRow(2);

​       nCell = nRow.getCell(3);

​       CellStyle productNoStyle = nCell.getCellStyle();

​       //获取第2行第4列的号码样式

​       nRow = sheet.getRow(2);

​       nCell = nRow.getCell(4);

​       CellStyle cnumberStyle = nCell.getCellStyle();

​       

​       nRow = sheet.getRow(2);

​       nCell = nRow.getCell(5);

​       CellStyle factoryStyle = nCell.getCellStyle();

​       

​       nRow = sheet.getRow(2);

​       nCell = nRow.getCell(6);

​       CellStyle extStyle = nCell.getCellStyle();

​       //获取第2行第7列的时间样式

​       nRow = sheet.getRow(2);

​       nCell = nRow.getCell(7);

​       CellStyle dateStyle = nCell.getCellStyle();

​       

​       nRow = sheet.getRow(2);

​       nCell = nRow.getCell(9);

​       CellStyle tradeTermsStyle = nCell.getCellStyle();

​       //3：往excel表中写数据

​       for(int i=0;i<oList.size();i++){

​           colNo = 1;

​           OutProduct op = oList.get(i);                 //获取每个出货表对象

​          

​           nRow = sheet.createRow(rowNo++);           //创建行

​           nRow.setHeightInPoints(24);                     //行高

​          

​           nCell = nRow.createCell(colNo++);            //创建单元格

​           nCell.setCellValue(op.getCustomName());

​           nCell.setCellStyle(customNameStyle);

​          

​           nCell = nRow.createCell(colNo++);

​           nCell.setCellValue(op.getContractNo());

​           nCell.setCellStyle(contractNoStyle);

​          

​           nCell = nRow.createCell(colNo++);

​           nCell.setCellValue(op.getProductNo());

​           nCell.setCellStyle(productNoStyle);

​          

​           nCell = nRow.createCell(colNo++);

​           nCell.setCellValue(op.getCnumber());

​           nCell.setCellStyle(cnumberStyle);

​          

​           nCell = nRow.createCell(colNo++);

​           nCell.setCellValue(op.getFactoryName());

​           nCell.setCellStyle(factoryStyle);

​          

​           nCell = nRow.createCell(colNo++);

​           nCell.setCellValue("附件");

 

​           List<String> extNameList = contractService.getExtName(op.getContractProductId());

​           String _extName = "";

​           if(extNameList!=null&&extNameList.size()>0){

​              for(String extName : extNameList){

​                  _extName += extName + "\n";            //换行符

​              }

​              _extName = _extName.substring(0,_extName.length()-1);      //去掉最后一个字符

​           }else{

​              _extName = "无";

​           }

​           nCell.setCellValue(_extName);

​           nCell.setCellStyle(extStyle);

​          

​           nCell = nRow.createCell(colNo++);

​           //nCell.setCellValue(UtilFuns.dateTimeFormat(op.getDeliveryPeriod()));    //利用工具类转类型，同时进行格式化

​           nCell.setCellValue(op.getDeliveryPeriod());

​           nCell.setCellStyle(dateStyle);

​          

​           nCell = nRow.createCell(colNo++);

​           //nCell.setCellValue(UtilFuns.dateTimeFormat(op.getShipTime()));

​           nCell.setCellValue(op.getShipTime());

​           nCell.setCellStyle(dateStyle);

​          

​           nCell = nRow.createCell(colNo++);

​           nCell.setCellValue(op.getTradeTerms());

​           nCell.setCellStyle(tradeTermsStyle);

​       }

​       

​       

​       DownloadUtil du = new DownloadUtil();

 

​       ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();        //生成流对象

​       wb.write(byteArrayOutputStream);

​       du.download(byteArrayOutputStream, response, "出货表.xls");      //弹出下载框，用户就可以直接下载

​    }
```



分析打印代码，读懂设计的重点；将原有的xls实现改造为xlsx实现。采用的poi模板打印，最后利用response进行的输出

 从总体看结构是，poiutil类中设置了细节性的设置信息，关于字体、图片、具体的线等。

在contractprint主体就是应用poiuti中 的格式设置和在本类中不同目标的设置样式按照预定样式填充货物数据信息，在填充完数据后进行打印。

### 3：插入图片

POI插入图片的原理，给定一个区域，图片自动伸缩到整个区域，为了图片不失真，要提前计算好单元格整个区域的高度和宽度。

HSSFPatriarch patriarch = sheet.createDrawingPatriarch();      //add picture

poiUtil.setPicture(wb, patriarch, rootPath+"make/xlsprint/logo.jpg", curRow, 2, curRow+4, 2);

### 4：合并单元格

合同单元格操作时，操作的是合并前的第一个单元格，它的样式，只能设置第一个

　　　　//合并单元格，新对象，不会覆盖合并的那些单元格，只是遮住，从rowNo行到rowNo行，从第0列到第六列 进行合并

​       sheet.addMergedRegion(new CellRangeAddress(rowNo, rowNo, 0, 6));          

### 5：其他

1） 插入线

2） 处理一款货物，还是两款货物，打印时样式

3） 换行

4） 人民币前缀样式

HSSFDataFormat format = wb.createDataFormat();

return format.getFormat("\"￥\"#,###,###.00"); // 设置格式

\#代表一位数值，这位数存在，就显示这位数字；这位数不存在，不显示；

0代表一位数值，这位数存在，就显示这位数字；这位数不存在，显示0；

 

5） 公式

​    nCell.setCellType(HSSFCell.CELL_TYPE_FORMULA);

​    nCell.setCellFormula("F"+String.valueOf(curRow)+"*H"+String.valueOf(curRow));

7） 分页

​           if(p>0){

​              sheet.setRowBreak(curRow++); //在第startRow行设置分页符

​           }

9） 单元格自适应高度

float height = poiUtil.getCellAutoHeight(printMap.get("Request"), 12f);       //自动高度





## 四：自定义工具类复用





