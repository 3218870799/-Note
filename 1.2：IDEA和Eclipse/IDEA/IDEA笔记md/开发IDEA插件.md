新建项目：

![image-20210706211109671](media/image-20210706211109671.png)

推荐使用社区版的IDEA，这样可以阅读IDEA的源码：

版本配置

1：gradle配置

```gradle
//默认会整成最新的JDk，这样导致有的项目不能用
sourceCompatibility = 1.8
//默认会整成最新的IDEA才能用，定义一个老一点的版本
intellij{
	version='2017.3'
	updateSinceUntilBuild=false
}
```

2：插件配置

resource-MATE-INF目录下的plugin.xml

具体看下边

3：打包成一个zip压缩包，

自己的话自己从硬盘上安装就可以了

4：发布上传

插件官网：https://plugins.jetbrains.com/

注册账号进行登录

直接点击自己头像下的upload plugin，然后选择刚打包好的zip文件

上传后，人家官方审核，审核通过即可从插件库下载到；



# 开发

## 1：给插件设置图标

在MATE-INF 文件夹下新建pluginicorn.svg ，名称固定，

## 2：配置

```xml
<!--兼容性配置-->
<idea-version since-build="171.0"></idea-version>
<description>
</description>
```

