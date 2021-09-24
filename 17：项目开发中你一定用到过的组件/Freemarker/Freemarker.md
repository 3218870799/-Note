1：引入Jar包

```xml
<!-- freemarker的坐标依赖 -->
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.23</version>
</dependency>

<!-- servlet-api的坐标依赖 -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.0.1</version>
</dependency>
```

2：新建模板

```html
<#--
    html 注释语法
        浏览器中查看源码可见

-->
<#--
    freemarker 注释语法
        浏览器中查看源码可见（开发中推荐使用）
        1.在freemarker中，html所有标签均适用
        2.js、css均适用，语法一致
-->
<#--接收数据-->
${msg}s
```

3：后台请求方法

```java
@WebServlet("/f01")
public class FreeMarker01 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //添加数据
        request.setAttribute("msg","Hello FreeMarker!");
        //请求跳转到ft1文件中
        request.getRequestDispatcher("template/f01.ftl").forward(request,response);
    }
}
```

## 语法

数据类型

## 常见指令

自定义变量

```html
<#assign str="hello">
${str} <br>
<#assign num=1 names=["zhangsan","lisi","wangwu"]>
${num} -- ${name?join(",")}
```

逻辑判断指令

```html
<#assign score = 80>
<#if score < 60>
   你个小渣渣！
   <#elseif score == 60>
   	分不在高，及格就行！
   <#elseif score gt 60 && score lt 80>
   	哎哟不错哦！
   <#else>
   	你很棒棒哦！
</#if>
<br>
<#-- 判断数据是否存在 -->
<#assign list="">
<#if list??>
   数据存在
   <#else>
   	数据不存在
</#if>
```

list遍历指令

```html
<#assign users3 = []>
<#-- 当序列没有数据项时，使用默认信息 -->
<#list users3 as user>
    ${user}
<#else>
    当前没有数据！
</#list>
```

自定义指令宏macro 



导入指令

```html
<#-- 导入命名空间 -->
<#import "commons.ftl" as common>
```

包含指令

```html
<#--包含指令(引入其他页面文件) include-->
<#--html文件-->
<#include "test.html">
<#--freemarker文件-->
<#include "test.ftl">
<#--text文件-->
<#include "test.txt">
```



## 工具类

```java
public class FreeMarkers {

	public static String renderString(String templateString, Map<String, ?> model) {
		try {
			StringWriter result = new StringWriter();
			Template t = new Template("name", new StringReader(templateString), new Configuration());
			t.process(model, result);
			return result.toString();
		} catch (Exception e) {
			throw Exceptions.unchecked(e);
		}
	}

	public static String renderTemplate(Template template, Object model) {
		try {
			StringWriter result = new StringWriter();
			template.process(model, result);
			return result.toString();
		} catch (Exception e) {
			throw Exceptions.unchecked(e);
		}
	}

	public static Configuration buildConfiguration(String directory) throws IOException {
		Configuration cfg = new Configuration();
		Resource path = new DefaultResourceLoader().getResource(directory);
		cfg.setDirectoryForTemplateLoading(path.getFile());
		return cfg;
	}
	
	public static void main(String[] args) throws IOException {
//		// renderString
//		Map<String, String> model = com.google.common.collect.Maps.newHashMap();
//		model.put("userName", "calvin");
//		String result = FreeMarkers.renderString("hello ${userName}", model);
//		System.out.println(result);
//		// renderTemplate
//		Configuration cfg = FreeMarkers.buildConfiguration("classpath:/");
//		Template template = cfg.getTemplate("testTemplate.ftl");
//		String result2 = FreeMarkers.renderTemplate(template, model);
//		System.out.println(result2);
		
//		Map<String, String> model = com.google.common.collect.Maps.newHashMap();
//		model.put("userName", "calvin");
//		String result = FreeMarkers.renderString("hello ${userName} ${r'${userName}'}", model);
//		System.out.println(result);
	}
	
}
```



