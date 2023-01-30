使用OkHttp发送请求主要分为以下几步骤：

- 创建OkHttpClient对象
- 创建Request对象
- 将Request 对象封装为Call
- 通过Call 来执行同步或异步请求，调用execute方法同步执行，调用enqueue方法异步执行



1：发送Get请求

```java
private OkHttpClient client = new OkHttpClient();

String api = "/api/files/1";  
String url = String.format("%s%s", BASE_URL, api);  
Request request = new Request.Builder()  
    .url(url)  
    .get()   
    .build();  
final Call call = client.newCall(request);  
Response response = call.execute();  
System.out.println(response.body().string()); 

```

异步Get请求

```java

        OkHttpClient client = new OkHttpClient();
        Request request = new Request.Builder()
                .url("http://localhost:8082/getName")
                .build();
        client.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }
            @Override
            public void onResponse(Call call, Response response) throws IOException {
                if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

                String s = new String(response.body().bytes());
                log.info("res--->:{}", s);
                log.info("异步请求执行完毕！！！");
            }
        });
```



2：发送POST请求

```java
private OkHttpClient client = new OkHttpClient();

    String api = "/api/user";  
    String url = String.format("%s%s", BASE_URL, api);  
    //请求参数  
    JSONObject json = new JSONObject();  
    json.put("name", "name");  
    RequestBody requestBody = RequestBody.create(MediaType.parse("application/json; charset=utf-8"),     String.valueOf(json));  
    Request request = new Request.Builder()  
            .url(url)  
            .post(requestBody) //post请求  
           .build();  
    final Call call = client.newCall(request);  
    Response response = call.execute();  
    System.out.println(response.body().string()); 
```



3：设置

```java

private OkHttpClient client = new OkHttpClient.Builder()  
        .connectTimeout(60, TimeUnit.SECONDS)//设置连接超时时间  
        .readTimeout(60, TimeUnit.SECONDS)//设置读取超时时间
    .writeTimeout(10, TimeUnit.SECONDS)//设置写超时
        .build();  
```

