一：安装

https://wkhtmltopdf.org/downloads.html

二：测试安装成功

打开cmd窗口，输入

```shell
wkhtmltopdf http://www.baidu.com/ D:test.pdf
```

三：程序中使用

```java

import java.util.concurrent.TimeUnit;

public class Html2PdfUtil {

    private static String windowWkPath = "E:\\wkhtmltox\\wkhtmltopdf\\bin\\wkhtmltopdf.exe";

    private static String linuxWkPath = "wkhtmltopdf";

    public static String getCommand(String htmlSrc, String pdfSrc) {
        StringBuilder cmd = new StringBuilder();
        String system = System.getProperty("os.name");
        if (system.toLowerCase().contains("windows")) {
            cmd.append(windowWkPath);
        } else if (system.toLowerCase().contains("linux") || system.toLowerCase().contains("mac")) {
            cmd.append(linuxWkPath);
        } else {
            return "";
        }
        cmd.append(" ");
        cmd.append("--orientation Landscape");// 横向展示
        cmd.append(" ");
        cmd.append("--window-status completed");
        cmd.append(" ");
        cmd.append("--javascript-delay 10000");
        cmd.append(" ");
        cmd.append("--debug-javascript");
        cmd.append(" ");
        cmd.append(htmlSrc);
        cmd.append(" ");
        cmd.append(pdfSrc);
        return cmd.toString();
    }

    public static String getLocalCommand(String htmlSrc, String pdfSrc) {
        StringBuilder cmd = new StringBuilder();
        String system = System.getProperty("os.name");
        if (system.toLowerCase().contains("windows")) {
            cmd.append(windowWkPath);
        } else if (system.toLowerCase().contains("linux") || system.toLowerCase().contains("mac")) {
            cmd.append(linuxWkPath);
        } else {
            return "";
        }
        cmd.append(" ");
        cmd.append(htmlSrc);
        cmd.append(" ");
        cmd.append(pdfSrc);
        return cmd.toString();
    }
	/**
	*	 工具方法，传入html文档和生成路径
	*/
    public static void html2PdfUtil(String htmlSrc, String pdfSrc) {
        String cmd = getCommand(htmlSrc, pdfSrc);
        System.out.print("执行的命令：" + cmd);
        try {
            Process proc = Runtime.getRuntime().exec(cmd);
            //获取进程的错误流
            HtmlToPdfInterceptor error = new HtmlToPdfInterceptor(proc.getErrorStream());
            //获取进程的标准输入流
            HtmlToPdfInterceptor output = new HtmlToPdfInterceptor(proc.getInputStream());
            error.start();
            output.start();
            proc.waitFor(6,TimeUnit.SECONDS);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void localHtml2PdfUtil(String htmlSrc, String pdfSrc) {
        String cmd = getLocalCommand(htmlSrc, pdfSrc);
        System.out.print("执行的命令：" + cmd);
        try {
            Process proc = Runtime.getRuntime().exec(cmd);
            HtmlToPdfInterceptor error = new HtmlToPdfInterceptor(proc.getErrorStream());
            HtmlToPdfInterceptor output = new HtmlToPdfInterceptor(proc.getInputStream());
            error.start();
            output.start();
            proc.waitFor();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

开启两个线程分别处理错误与输出

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;

public class HtmlToPdfInterceptor extends Thread {
    private InputStream is;

    public HtmlToPdfInterceptor(InputStream is){
        this.is = is;
    }

    public void run(){
        try{
            InputStreamReader isr = new InputStreamReader(is, "utf-8");
            BufferedReader br = new BufferedReader(isr);
            String line = null;
            while ((line = br.readLine()) != null) {
                System.out.println(line.toString()); //输出内容
            }
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}
```





