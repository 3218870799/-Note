# 简介

POI比价耗内存，EasyExcel减少内存溢出的可能性，POI是净文件直接加载到内存，然后再写入文件，而EasyExcel是一行行的将文件读，一行行的写入到文件中去；

小文件使用POI，大文件使用EasyExcel；



# 使用：

1：添加依赖

```xml
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.39</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>easyexcel</artifactId>
            <version>2.1.6</version>
        </dependency>
        
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
        
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
```



## 读

编写导出实体类，字段和顺序需要和表格一一对应，实体类的属性位置就是表格从左到右的属性

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    /**
     * 学生姓名
     */
    private String name;
    /**
     * 学生出生日期
     */
    private Date birthday;
    /**
     * 学生性别
     */
    private String gender;

    /**
     * id
     */
    private String id;
}
```

读取Excel文件：

默认一行行的读取excel，所以需要创建excel一行一行的回调监听器

```java
public class ExcelTest {
    private static final Logger LOGGER = LoggerFactory.getLogger(ExcelTest.class);
    /**
     * 工作簿：bookwork
     * 工作表：sheet
     */
    @Test
    public void test01() {
        // 有个很重要的点 DemoDataListener 不能被spring管理，要每次读取excel都要new,然后里面用到spring可以构造方法传进去
        //获得工作簿对象
        /*
         EasyExcel.read()参数：
            pathName  		文件路径；"d:\\学员信息表.xlsx"
        	head			每行数据对应的实体；Student.class
        	readListener	读监听器，每读一样就会调用一次该监听器的invoke方法
         */
        ExcelReaderBuilder excelReaderBuilder = EasyExcel.read("学员信息表.xlsx", Student.class, new StudentReadListener());
        //获取一个工作表
        ExcelReaderSheetBuilder sheet = excelReaderBuilder.sheet();
        //读取工作表内容:sheet方法参数：工作表的顺序号（从0开始）或者工作表的名字，不传默认为0
        sheet.doRead();
    }}
```

创建Excel的监听器，用于处理读取产生的数据

在读的时候，每读一行，就会自动调用监听器的invoke方法，并且把读取的内容自动封装成了一个对象

```java
public class StudentReadListener extends AnalysisEventListener<Student> {

    private static final Logger LOGGER = LoggerFactory.getLogger(StudentReadListener.class);

    /**
     * 每读一行会自动调用这个方法
     * @param student 读取的内容自动封装成了一个对象
     * @param context
     */
    @Override
    public void invoke(Student student, AnalysisContext context) {
        LOGGER.info("解析到一条数据:{}", JSON.toJSONString(student));
        System.out.println("student = " + student);
    }
    // 全部读完之后，会调用该方法
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {

    }}
```

## 写

```java
@Test
    public void test02(){
        /*
         * 工作簿对象
         * @param pathName 文件路径名称
         * @param head 封装写出的数据实体的类型
         * @return 写出工作表对象
         */
        ExcelWriterBuilder write = EasyExcel.write("学员信息表-write.xlsx", Student.class);
        //工作表对象
        ExcelWriterSheetBuilder sheet = write.sheet();
        //需要写出的数据：
        List<Student> students = initData();
        //写出
        sheet.doWrite(students);
    }
    /**
     * 初始好数据
     */
    private static List<Student> initData() {
        ArrayList<Student> students = new ArrayList<Student>();
        Student data = new Student();
        for (int i = 0; i < 10; i++) {
            data.setName("学号0" + i);
            data.setBirthday(new Date());
            data.setGender("男");
            students.add(data);
        }
        return students;
    }
```

实体类要加上列头注解：

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
//@ColumnWidth(20) //列宽
//@HeadRowHeight(30)  //列头行高
//@ContentRowHeight 内容行高
public class Student {
    /**
     * id
     */
    @ExcelProperty(value = "学生编号")
    private String id;
    /**
     * 学生姓名
     */
    //@ExcelProperty(value = "学生姓名",index = 3) index是表格的索引地址
    private String name;

    /**
     * 学生出生日期
     */
    //@ColumnWidth(20)
    //@DateTimeFormat("yyyy-MM-dd")  设置日期格式
    @ExcelProperty(value = "出生日期", index = 1)
    @ColumnWidth(20) //列宽
    private Date birthday;
    /**
     * 学生性别
     */
    private String gender;

    /**
     * 忽略这个字段
     */
    @ExcelIgnore
    private String ignore;}
```

## 文件上传

（1）添加依赖

```xml
<dependencies>

    
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.39</version>
    </dependency>

    
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>easyexcel</artifactId>
        <version>2.1.6</version>
    </dependency>
    
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.10</version>
    </dependency>
    
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
    
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.5</version>
    </dependency>
    
    <dependency>
        <groupId>commons-fileupload</groupId>
        <artifactId>commons-fileupload</artifactId>
        <version>1.4</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency></dependencies>
```

（2）Student实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    /**
     * id
     */
    @ExcelIgnore
    private String id;
    /**
     * 学生姓名
     */
    private String name;

    /**
     * 学生出生日期
     */
    private Date birthday;
    /**
     * 学生性别
     */
    private String gender;
}
```

(3)Service

```java
@Service("studentService")
public class StudentServiceImpl implements StudentService {
    private int i = 0;
    @Override
    public void save(List<Student> students) {
        for (Student student : students) {
            System.out.println(i++ + "学生" + "号：" + student);
        }
    }}
```

(4)：WebListener

```java
@Component
@Scope("prototype")
public class WebStudentReadListener extends AnalysisEventListener<Student> {

    private static final Logger LOGGER = LoggerFactory.getLogger(WebStudentReadListener.class);

    @Autowired
    private StudentService studentService;


    private final int BATCH_SAVE_NUM = 5;
    ArrayList<Student> students = new ArrayList<>();

    @Override
    public void invoke(Student student, AnalysisContext context) {
        LOGGER.info("解析到一条数据:{}", JSON.toJSONString(student));
        students.add(student);
        if (students.size() % BATCH_SAVE_NUM == 0) {
            studentService.save(students);
            students.clear();
        }

    }

    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {

    }}
```

(5)Controller

```java
@Controller
@RestController
public class WebUploadAndDownload {
    @Autowired
    private WebStudentReadListener webStudentReadListener;
    /**
     * 文件上传
     * 1. 编写excel中每一行对应的实体类
     * 2. 由于默认异步读取excel，所以需要逐行读取的回调监听器
     * 3. 开始读取Excel
     */
    @PostMapping("upload")
    public String upload(MultipartFile multipartFile) throws IOException{
        ExcelReaderBuilder read = EasyExcel.read(multipartFile.getInputStream(), Student.class, webStudentReadListener);
        read.sheet().doRead();
        return "success";
    }}
```

## 文件下载

```java
@GetMapping("download")
public void download(HttpServletResponse response) throws IOException {
    response.setContentType("application/vnd.ms-excel");
    response.setCharacterEncoding("utf-8");
    // 防止中文乱码
    String fileName = URLEncoder.encode("测试", "UTF-8");
    response.setHeader("Content-Disposition", "attachment; filename*=UTF-8''" + fileName + ".xlsx");


    ExcelWriterBuilder write = EasyExcel.write(response.getOutputStream(), Student.class);
    write.sheet("模板").doWrite(initData());
}
/**
 * 初始好数据
 */
private static List<Student> initData() {
    ArrayList<Student> students = new ArrayList<Student>();
    Student data = new Student();
    for (int i = 0; i < 10; i++) {
        data.setName("学号0" + i);
        data.setBirthday(new Date());
        data.setGender("男");
        students.add(data);
    }
    return students;
}
```

6：填充

给模板文件填充数据

填充一组数据：

```java
public static void main(String[] args) {
    // 加载模板
    InputStream templateFile = FillData.class.getClassLoader().getResourceAsStream(
            "fill_data_template1" +
            ".xlsx");

    // 写入文件
    String targetFileName = "单组数据填充.xlsx";

    // 准备对象数据填充
    FillData fillData = new FillData();
    fillData.setName("");
    fillData.setAge(10);


    // 生成工作簿对象
    ExcelWriterBuilder workBookWriter = EasyExcel.write(targetFileName).withTemplate(templateFile);

    // 获取工作表并填充
    //workBookWriter.sheet().doFill(fillData);

    // 使用Map数据填充
    HashMap<String, String> mapFillData = new HashMap<>();
    mapFillData.put("name", "Map");
    mapFillData.put("age", "11");

    // 获取第一个工作表填充并自动关闭流
    workBookWriter.sheet().doFill(mapFillData);}
```

填充多组数据

```java
public static void main(String[] args) {
    // 加载模板
    InputStream templateFile = FillData.class.getClassLoader().getResourceAsStream(
            "fill_data_template2.xlsx");

    // 写入文件
    String targetFileName = "多组数据填充.xlsx";

    List<FillData> fillDatas = initData();

    // 生成工作簿对象
    ExcelWriterBuilder workBookWriter =
            EasyExcel.write(targetFileName).withTemplate(templateFile);

    // 获取第一个工作表填充并自动关闭流
    workBookWriter.sheet().doFill(fillDatas);}
```

组合填充：

```java
public static void main(String[] args) {

    // 加载模板
    InputStream templateFile = FillData.class.getClassLoader().getResourceAsStream(
            "fill_data_template3.xlsx");

    // 目标文件
    String targetFileName = "组合数据填充.xlsx";

    List<FillData> fillDatas = initData();

    // 生成工作簿对象
    ExcelWriter excelWriter = EasyExcel.write(targetFileName).withTemplate(templateFile).build();

    // 生成工作表对象
    WriteSheet writeSheet = EasyExcel.writerSheet().build();

    // 组合填充时，因为多组填充的数据量不确定，需要在多组填充完之后另起一行
    FillConfig fillConfig = FillConfig.builder().forceNewRow(true).build();

    // 填充并换行
    excelWriter.fill(fillDatas, fillConfig, writeSheet);

    HashMap<String, String> otherData = new HashMap<>();
    otherData.put("date", "2020-03-14");
    otherData.put("total", "100");
    excelWriter.fill(otherData, writeSheet);

    // 关闭
    excelWriter.finish();}
```

水平填充：

```java
public static void main(String[] args) {

    // 加载模板
    InputStream templateFile = FillData.class.getClassLoader().getResourceAsStream(
            "fill_data_template4.xlsx");

    // 写入文件
    String targetFileName = "easyExcelDemo\\水平数据填充.xlsx";

    List<FillData> fillDatas = initData();

    // 生成工作簿对象
    ExcelWriter excelWriter = EasyExcel.write(targetFileName).withTemplate(templateFile).build();

    // 生成工作表对象
    WriteSheet writeSheet = EasyExcel.writerSheet().build();


    // 组合填充时，因为多组填充的数据量不确定，需要在多组填充完之后另起一行
    FillConfig fillConfig = FillConfig.builder().direction(WriteDirectionEnum.HORIZONTAL).build();

    // 填充
    excelWriter.fill(fillDatas, fillConfig, writeSheet);

    HashMap<String, String> otherData = new HashMap<>();
    otherData.put("date", "2020-03-14");
    otherData.put("total", "100");
    excelWriter.fill(otherData, writeSheet);

    // 关闭
    excelWriter.finish();}
```

