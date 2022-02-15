# 使用查看

给README添加小徽章

- https://shields.io/
- https://badgen.net/
- https://forthebadge.com/
- https://badge.fury.io/
- https://github.com/boennemann/badges

## 常用词含义

- watch：会持续收到该项目的动态
- fork：复制某个仓库到自己的Github仓库中
- star：可以理解为点赞
- clone：将项目下载至本地
- follow：关注你感兴趣的作者，会收到他们的动态

 

## in关键字限制搜索范围

- 公式：`XXX关键字 in:name 或 description 或 readme`
- xxx in:name   项目名称含有XXX的
- xxx in:description   项目描述含有XXX的
- xxx in:readme   项目的readme文件中包含XXX的
- 组合使用
  - xxx   in:name,readme    项目的名称和readme中包含xxx的



## stars或fork数量关键字查找

- 公式：
  - `xxx关键字  stars 通配符`  :>  或者 :>=
  - 区间范围数字：  `stars:数字1..数字2`
- 案例
  - 查找stars数大于等于5000的Springboot项目：springboot  stars:>=5000
  - 查找forks数在1000~2000之间的springboot项目：springboot forks:1000..5000
- 组合使用
  - 查找star大于1000，fork数在500到1000：`springboot stars:>1000 forks:500..1000`



## awesome加强搜索

- 公式：`awesome 关键字`：awesome系列，一般用来收集学习、工具、书籍类相关的项目
- 搜索优秀的redis相关的项目，包括框架，教程等  awesome redis



## 高亮显示某行代码

- 一行：地址后面紧跟  #L10
  - `https://github.com/moxi624/mogu_blog_v2/blob/master/mogu_admin/pom.xml#L13`
- 多行：地址后面紧跟 #Lx - #Ln
  - `https://github.com/moxi624/mogu_blog_v2/blob/master/mogu_admin/pom.xml#L13-L30`

## 项目内搜索

- 使用英文字母 `t` ,开启项目内搜索

![image-20200326212650322](media/image-20200326212650322.png)



## 搜索某个地区内的大佬

- location：地区
- language：语言
- 例如：`location:beijing language:java`





# GitHub Pages搭建网站

新建仓库，创建个人站点，仓库名必须是 【用户名.github.io】

在仓库下新建index.html文件即可。只支持静态网页。







# 为开源项目贡献

## 原则

一：做有价值的事

二：不要给别人添麻烦，可以让别人直接合并。一个提交只解决一个问题。

三：先搜索，不要走撞车的问题

四：使用工具



## 例子

为Gradle修改Java8语法

将需要更改关心的变成Error

![image-20210207213628693](media/image-20210207213628693.png)

然后在项目上右键——>Analuze——>Inspect Code

点击错误，使用 ` Alt + Enter ` 修改



为SpringBoot贡献代码

先 ` fork ` ,下载到本地



写PR 

```
i found some code to simplify when using IDEA to inspect the Code,so i submit a PR here,Thanks!
```

签署贡献协议，签署个人



为Guava









Java中一切的操作都是一个拼接命令行参数而已。



草案：

Issue讨论



谷歌文档



Effective.java



远程仓库资源

remote-working：中国远程工作资料大全

网站：一早一晚



社区：https://forum.westack.live

一定是自己使用熟悉的项目







