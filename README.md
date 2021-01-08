<center><h1>程序猿成长笔记</h1></center>

------
<center><img src="https://img.shields.io/badge/Note-grow-green"></img><img src="https://img.shields.io/github/repo-size/3218870799/-Note?style=plastic"></img><img src="https://img.shields.io/badge/role-qcxiao-red"></img></center>
------

## >> 说明

:a:本项目是个人学习的笔记，包含一些工具的安装与使用，基础的知识点的整理，以及工作学习中的经验。



:b:一些知识梳理我会放在各个文件夹中，是学习某项技术时**必须掌握的原理以及使用技巧。**

对于一些综合技术的探索，或者对某一功能的开发遇到的问题探索，个人经验的总结，我会放在**《我的博客》**文件中。



:cupid:如果也能各位有些许参考作用，我将十分荣幸！

------

由于目前内容越来越多，一篇的篇幅过长，担心读者对于内容的查看没有过多时间，但是分篇破坏其完整性，故更改目录结构进行中……

如果导致目录过长，望请谅解！

------

入手 GitHub 很早，但不够专业，排版什么也没有遵循 GFM 规范，也没有搞图床。从以后开始注意，但是以前的工程量太大，我也不打算进行修改了。

------



# 目录

## 我的博客

### 《基于Redis的分布式锁》

### 《业务ID的生成方式》



## 目录大纲

| 目录                        | 子目录                                                       | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 数据结构与算法👶             | 1：数据结构与算法<br />2：LeetCode分类题解与常见算法题       | 以Java描述的常用数据结构与基本算法的操作，常见刷题笔记       |
| 👆计算机网络                 |                                                              |                                                              |
| 😗操作系统                   |                                                              |                                                              |
| 🍼版本控制工具               | 1：Git<br />2：SVN<br />3：GitHub使用技巧                    | git与Github的使用，SVN的使用，工具小乌龟                     |
| 🐤开发工具                   | 1：IDEA<br />2：Eclipse                                      | 选一即可，本人是从Eclipse换到IDEA的，故Eclipse笔记也很久没更新了。 |
| 🚼正则表达式                 |                                                              |                                                              |
| 🎈Java基础:                  |                                                              | java的基础知识，源码探索以及对新版本的实验研究               |
| 🌵前端的基础知识             | 1：ES6<br />2：VUE                                           | 由于本人不是专业前端，对前端的学习不是很多，只是部分自己用到的 |
| 🤳服务器                     | 1：Tomact<br />2：Nginx                                      |                                                              |
| 🍺 JVM                       |                                                              |                                                              |
| 💃项目管理工具               | 1：Maven<br />2：Nexus搭建<br />3：Gradle                    |                                                              |
| 👯关系型数据库               | 1：Mysql<br />2：Oracle                                      |                                                              |
| 👊非关系型数据库             | 1：Redis<br />2：Ehcahce<br />3：MongoDB                     |                                                              |
| 🥇Java主流框架               | 1：SpringMVC<br />2：Spring<br />3：Mybatis<br />            |                                                              |
| 👆分布式应用                 | 1：SpringBoot<br />2：消息队列<br />Kafka<br />RabbitMQ<br /><br />3：Zookeeper<br />4：Dubbo<br />5：SpringCloud<br />6：SpringCloudAlibaba |                                                              |
| 🥉Linux                      |                                                              |                                                              |
| 🌴项目开发中你一定用过的组件 | 安全验证，搜索，……                                           | 内容较多，详情请看具体目录内容                               |
| 📫接口                       | 1：Webservice<br />2：Swagger<br />3：JApiDocs               |                                                              |
| ✌️大数据                     | 1：Hadoop<br />2：HBase<br />3：Hive<br />4：Spark           |                                                              |
| 网络安全                    |                                                              |                                                              |
| 🙊设计模式                   |                                                              | 常用设计模式的基本概念与常见应用                             |
| 🐍Python                     |                                                              |                                                              |
| Ⓜ️机器学习                   |                                                              |                                                              |
| 🐭Go                         |                                                              |                                                              |
| 📦项目管理                   | 1：Jenkins<br />2：SonarQube                                 |                                                              |
| 我的博客                    |                                                              |                                                              |



## :baby:数据结构与算法

说明：以Java描述的常用数据结构与基本算法的操作，常见刷题笔记

- [概述](https://github.com/3218870799/-Note/blob/main/0.0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95.md#%E7%AC%AC%E4%B8%80%E7%AB%A0%E6%A6%82%E8%BF%B0)

- [数组](https://github.com/3218870799/-Note/blob/main/0.0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95.md#%E7%AC%AC%E4%BA%8C%E7%AB%A0%E6%95%B0%E7%BB%84)

- [队列](https://github.com/3218870799/-Note/blob/main/0.0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95.md#%E7%AC%AC%E4%B8%89%E7%AB%A0%E9%98%9F%E5%88%97)

- 线性表

- 栈

- 树

- 哈希表

- Set集合

- Map

- 堆

- 串

- 图

- 查找

- 递归

- 排序

- 算法知识

  - 并查集
  - 位运算
  - 数论
  - 动态规划
  - KMP
  - 布隆过滤器
  - 树状数组
  - 贪心
  - 分治
  - 回溯



## :100:LeetCode分类题解

- 数组
- 字符串
- 链表
- 二叉树
- 图
- 哈希表
- 动态规划
- 并查集

## :point_up_2:计算机网络



## :kissing:操作系统



## :baby_bottle:版本控制工具

### Git



### SVN



## :baby_chick:开发工具

选一即可，本人是从Eclipse换到IDEA的，故Eclipse笔记也很久没更新了。

### Java开发

#### IDEA

#### Eclipse



##  :baby_symbol:正则表达式






## :balloon:Java基础:

说明：java的基础知识，源码探索以及对新版本的实验研究

- [一：基础语法](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#一基础语法)


- [二：常用API](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%BA%8C%E5%B8%B8%E7%94%A8api)


- [三：集合](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%B8%89%E9%9B%86%E5%90%88)

- [四：异常](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E5%9B%9B%E5%BC%82%E5%B8%B8)

- [五：多线程](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%BA%94%E5%A4%9A%E7%BA%BF%E7%A8%8B)

- [六：IO与NIO](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E5%85%ADio%E4%B8%8Enio)

- [七：网络编程](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%B8%83%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B)

- [八：JDBC](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E5%85%ABjdbc)

- [九：特性](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%B9%9D%E7%89%B9%E6%80%A7)

- [Java8](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java8)

- [Java9](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java9)

- [Java10](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java10)

- [Java11](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java11)

- [Java12](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java12)

- [Java13](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java13)



## :cactus:前端的基础知识

### ES6



### VUE



JavaWeb开发

## :selfie:服务器

### Tomact

### Nginx



## :beer: JVM

- JVM内存结构
- 垃圾回收机制
- 类加载与字节码技术
- 内存模型JMM
- 常用JVM配置参数
- VisualVM的使用



## :dancer:项目管理工具

### Maven

### Nexus搭建

### Gradle



## :dancers:关系型数据库

### MYSQL

### Oracle

## :facepunch:非关系型数据库

### Redis

### Ehcahce

### Mongdb

## :1st_place_medal:Java主流框架

### SpringMVC

### Spring

### Mybatis

### Hibernate

## :point_up_2:分布式应用

### SpringBoot

### 消息队列

### Zookeeper

### Dubbo

### SpringCloud

### SpringCloudAlibaba



## :3rd_place_medal:Linux



## :palm_tree:项目开发中你一定用过的组件



### 检索

#### 1：Lucene全文检索

#### 2：solr全文检索服务器

#### 3：ElasticSearch



### 安全验证

#### 1：shiro安全验证

#### 2：SpringSecurity



### 业务流程管理Activiti



### 自动代码生成



### 作业调度Quartz



### 单点登录



### 模板引擎freemarker



## :shaved_ice:Netty

异步事件驱动的网络应用程序框架



## :mailbox:接口

### webservice

### Swagger

### JApiDocs

## :v:大数据

### Hadoop

###  HBase

###  Hive

### spark

## 网络安全





## :speak_no_evil:设计模式

- 单例模式



## :snake:Python







## :m:机器学习  

##  

## SpringSession



## :mouse:Go



## :package:项目管理



### 1：持续集成Jenkins



### 2：代码审查SonarQube













环境搭建

VMware   

架构设计





> > :cactus:已迁移整理至Github项目-Note，欢迎浏览提出建议

> >:dancer:[https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md](https://github.com/3218870799/-Note/blob/main/2.0.Java基础/Java基础/Java基础.md)

> >:athletic_shoe:建议下载使用Typora软件进行查看与编辑，效果更佳哦！



https://github.com/3218870799/-Note/blob/main/0.0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95.md

> > :haircut:**由于个人原因，工作后不如在学校的时间充裕，对于博客的排版等问题精力不足，**

> > **故将自己的学习笔记整理成Word文档和MD方式上传至GitHub，**

> > :dancing_women:**博客如果有什么新奇的想法依然还是会找时间更新的，毕竟园友还是很神通的都。**



> > :cake:**个人笔记项目地址：https://github.com/3218870799/-Note**