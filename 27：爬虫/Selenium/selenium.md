

# Java操作

1：引入

jar包下载：http://selenium-release.storage.googleapis.com/index.html

Maven：https://mvnrepository.com/artifact/org.seleniumhq.selenium?__cf_chl_captcha_tk__=pmd_zs_bTxEqRkCXDamPXIYRTn8O_JqqGPlc5VkfnOWFeGw-1631792499-0-gqNtZGzNAxCjcnBszQnR

```xml
       <!-- selenium-java -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>3.4.0</version>
        </dependency>

```



2：下载Chrome对应的驱动：http://chromedriver.storage.googleapis.com/index.html

## 语法

https://blog.csdn.net/qq_22003641/article/details/79137327

hello World

```java
WebDriver driver = new ChromeDriver();
driver.get("http://www.baidu.com");

String title = driver.getTitle();

driver.close();
```



获取input内的值

```java
WebElement.getText();
```

获取属性的值

```java
WebElement.getAttribute("value");
```



关闭当前窗口

```java
driver.close();
```

关闭浏览器

```java
driver.quit();
```

使用service优化：

```java
package zt.o.cbsreportservice.utils;


import org.openqa.selenium.PageLoadStrategy;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeDriverService;
import org.openqa.selenium.chrome.ChromeOptions;

import java.io.IOException;

public class WebDriverUtil {

    private static ChromeDriverService service;

    private static String WINDOW_CHROME_DRIVER_PATH = "F:\\Chrome\\chromedriver_win32\\chromedriver.exe";
    private static String LINUX_CHROME_DRIVER_PATH = "/usr/bin/chromedriver";

    //ChromeDriverService封装构建过程，程序结束时结束是生命
    static {
        try {
            System.setProperty("webdriver.chrome.driver", getToolPath());
            service = new ChromeDriverService.Builder()
                    .usingAnyFreePort()
                    .build();
            service.start();
            Runtime.getRuntime().addShutdownHook(new Thread(service::stop));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static ChromeDriverService getServiceInstance(){
        return service;
    }

    public static WebDriver getDriver(){
        //添加浏览器参数
        ChromeOptions chromeOptions = new ChromeOptions();
        //无头模式
        chromeOptions.addArguments("--headless");
        //禁用GPU加速
        chromeOptions.addArguments("--disable-gpu");
        //解决DevToolsActivePort文件不存在的报错，用以root用户权限运行
        chromeOptions.addArguments("--no-sandbox");
        chromeOptions.addArguments("--disable-dev-shm-usage");
        //浏览器最大化
        chromeOptions.addArguments("--start-maximized");
        //NORMAL策略：等待整个界面加载完成
        chromeOptions.setPageLoadStrategy(PageLoadStrategy.NORMAL);

        WebDriver driver = new ChromeDriver(service, chromeOptions);
        return driver;
    }

    /**
     * 获取驱动的路径
     * @return
     */
    private static String getToolPath() {
        String system = System.getProperty("os.name");
        if (system.toLowerCase().contains("windows")) {
            return WINDOW_CHROME_DRIVER_PATH;
        } else if (system.toLowerCase().contains("linux") || system.toLowerCase().contains("mac")) {
            return LINUX_CHROME_DRIVER_PATH;
        }
        return "";
    }
}

```



