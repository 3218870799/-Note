# 一：简介

官网：http://hc.apache.org/

使用场景：多系统间接口交互，爬虫；



JDK原生发送http请求

```java
//请求网页

String urlStr = "https://www.baidu.com/"
URL url = new URL(urlStr);
URLConnection urlConnection = url.openConnection();
HttpURLConnection httpURLConnection = (HttpURLConnection)urlConnection;
//设置请求类型：请求行，请求头，请求体
httpURLConnection.setRequestMethod("GET");
httpURLConnection.set
//获取httpURLConnection的输入流,然后输出
try{
}catch()

```



分析：

# 二：使用

请求头：

```txt
User-Agent:标识使用的什么浏览器，请求时加入解决被认为不是真人行为
Referer:value填请求网站网址，防盗链;

```

1：使用httpClient发送GET请求

```java
public static String sendGet(String url, Map<String,String> params){
    CloseableHttpClient httpClient = null;
    HttpGet httpGet = null;
    CloseableHttpResponse response = null;
    String result = "";
    try {
        httpClient = HttpClientBuilder.create().build();
        httpGet = new HttpGet(url);
        // 设置参数
        if (null != params && params.size() > 0){
            List<BasicNameValuePair> pairList = new ArrayList<>();
            params.forEach((x,y) -> pairList.add(new BasicNameValuePair(x,y)));
            UrlEncodedFormEntity urlEncodedFormEntity = new UrlEncodedFormEntity(pairList,"utf-8");
            // 将参数转成page=1&limit=5格式
            String param = EntityUtils.toString(urlEncodedFormEntity, "utf-8");
            httpGet = new HttpGet(url+"?"+param);
        }
        response = httpClient.execute(httpGet);
        result = EntityUtils.toString(response.getEntity());
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        close(response,httpGet,httpClient);
    }
    return result;
}
```

2：发送Post请求

```java
public static String sendPost(String url, Map<String,String> params){
    CloseableHttpClient httpClient = null;
    HttpPost httpPost = null;
    CloseableHttpResponse response = null;
    String result = "";
    try {
        httpClient = HttpClientBuilder.create().build();
        httpPost = new HttpPost(url);
        // 设置参数
        if (null != params && params.size() > 0){
            List<BasicNameValuePair> pairList = new ArrayList<>();
            params.forEach((x,y) -> pairList.add(new BasicNameValuePair(x,y)));
            UrlEncodedFormEntity urlEncodedFormEntity = new UrlEncodedFormEntity(pairList,"utf-8");
            httpPost.setEntity(urlEncodedFormEntity);
        }
        response = httpClient.execute(httpPost);
        result = EntityUtils.toString(response.getEntity());
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        close(response,httpPost,httpClient);
    }
    return result;
}
```

3：post以json格式传递数据

```java
public static String sendPostJson(String url, Map<String,String> params){
        CloseableHttpClient httpClient = null;
        HttpPost httpPost = null;
        CloseableHttpResponse response = null;
        String result = "";
        try {
            httpClient = HttpClientBuilder.create().build();
            httpPost = new HttpPost(url);
            // 设置参数
            if (null != params && params.size() > 0){
                String paramJson = JSONObject.toJSONString(params);
                StringEntity stringEntity = new StringEntity(paramJson,"utf-8");
                stringEntity.setContentType("application/json;charset=utf-8");
                httpPost.setEntity(stringEntity);
            }
            response = httpClient.execute(httpPost);
            result = EntityUtils.toString(response.getEntity());
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            close(response,httpPost,httpClient);
        }
        return result;
    }

```



3：设置

```java
//设置超时时间 
private RequestConfig requestConfig =  RequestConfig.custom()  
        .setSocketTimeout(60 * 1000)  
        .setConnectTimeout(60 * 1000).build();

HttpGet httpGet = new HttpGet(url); 
httpGet.setConfig(requestConfig);

```

