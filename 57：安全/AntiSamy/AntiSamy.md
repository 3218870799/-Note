策略文件：策略文件定义的严格与否，决定了AntiSamy对XSS漏洞的防御效果； 

在包里面有，可以直接赋值过来就行 

其实有些浏览器也已经自己集成了一些XSS攻击的防范；

实现就是通过一个过滤器实现的：

```java
public class XSSFilter implement Filter{
    @Override
    public void doFilter(ServletRequest servletRequest,ServletResponse servletResponse,FilterChain filterChain){
        HttpServletRequest request = (HttpServletRequest)servletRequest;
        //传入重写后的Request ，直接放行
        filterChain.doFilter(new XssRequestWrapper(request).servletResponse);
    } 
}
```

XssRequestWrapper.java：包装对象，处理参数

```java
public class XssRequestWrapper extends HttpServletRequestWrapper {
    privatestatic String antisamyPath = XssRequestWrapper.
        
	public XssRequestWrapper(HttpServletRequest request){
		super(request) ;
    }
		public String[] getParameterValues(String name){
           String parameterValues =  super.getParameterValues(name);
            //处理参数重新封装到新数组中返回
            int length = parameterValues.length;
            String newArray = new String(length);
            for(int i = 0;i<length;i++){
                
            }
            
            
            
			system. out.println(name + " " + super.getParameterValues(name)) ;
            return super.getParameterValues (name) ;
        }
    //通过AntiSamy框架过滤字符
    public String cleanXss(String text){
        
    }
    
}
```

配置类注册过滤器：

