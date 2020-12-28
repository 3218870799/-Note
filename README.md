# 说明

:a:本项目是个人学习的笔记，包含一些工具的安装与使用，基础的知识点的整理，以及工作学习中的经验。



:b:一些知识梳理我会放在各个文件夹中，是学习某项技术时**必须掌握的原理以及使用技巧。**

对于一些综合技术的探索，或者对某一功能的开发遇到的问题探索，个人经验的总结，我会放在**《我的博客》**文件中。



:cupid:如果也能各位有些许参考作用，我将十分荣幸！



由于目前内容越来越多，一篇的篇幅过长，担心读者对于内容的查看没有过多时间，但是分篇破坏其完整性，故更改目录结构进行中……

如果导致目录过长，望请谅解！

# 目录

## 总述

| 序号  | 标题                      | 说明                                                   | 子章节 |
| ----- | ------------------------- | ------------------------------------------------------ | ------ |
| 0.0   | 数据结构与算法:baby:      | 以Java描述的常用数据结构与基本算法的操作，常见刷题笔记 |        |
| 0.1   | 计算机网络                |                                                        |        |
| 0.2   | 操作系统                  |                                                        |        |
| 1.1   | 版本控制工具:baby_bottle: | git与Github的使用，SVN的使用，工具小乌龟               |        |
| 1.2   | 开发工具:baby_chick:      | 主要针对Java开发的工具使用技巧                         |        |
| 2.0.0 | 正则表达式:baby_symbol:   |                                                        |        |
| 2.0   | Java语言基础:balloon:     | java的基础知识，源码探索以及对新版本的实验研究         |        |
| 2.1   | 前端的基础知识            |                                                        |        |
| 2.2   | JavaWeb开发               |                                                        |        |
| 2.3   | 服务器Tomact 与Nginx      |                                                        |        |
| 3     | JVM:beer:                 |                                                        |        |
| 4     | 项目管理工具              | Maven，Gradle，Nexus使用                               |        |
| 6     | 关系型数据库:dancer:      | MYSQL与Oracle                                          |        |
| 7     | 非关系型数据库            | Redis，Ehcahce，Mongdb                                 |        |
| 8     | SpringMVC                 |                                                        |        |
| 9     | Spring                    |                                                        |        |
| 10    | Mybatis                   |                                                        |        |
| 11    | Hibernate                 |                                                        |        |
| 12    | SpringBoot                |                                                        |        |
| 13    | Dubbo                     |                                                        |        |
| 14    | SpringCloud               |                                                        |        |
| 14    | 消息队列                  |                                                        |        |
| 15    | Zookeeper                 |                                                        |        |
| 16    | Linux                     |                                                        |        |
| 17.1  | Lucene全文检索            |                                                        |        |
| 17.2  | solr全文检索服务器        |                                                        |        |
| 17.3  | shiro安全验证             |                                                        |        |
| 17.4  | 业务流程管理Activiti      |                                                        |        |
| 17.5  | 统计分析                  |                                                        |        |
| 17.6  | 作业调度Quartz            |                                                        |        |
| 17.7  | Netty:shaved_ice:         | 异步事件驱动的网络应用程序框架                         |        |
| 18    | webservice                |                                                        |        |
| 19    | Hadoop                    |                                                        |        |
| 20    | HBase                     |                                                        |        |
| 21    | Hive                      |                                                        |        |
| 22    | Spark                     |                                                        |        |
| 23    | 网络安全                  |                                                        |        |
| 24    | 设计模式                  |                                                        |        |
| 25    | 机器学习概述              |                                                        |        |
| 26    | Python基础                |                                                        |        |
| 27    | 自动代码生成              |                                                        |        |
| 31    | SpringSession             |                                                        |        |
| 33    | 项目管理                  |                                                        |        |
| 34    | Go语言基础                |                                                        |        |
| 35    | 环境搭建                  |                                                        |        |
| 36    | VMware                    |                                                        |        |
| 37    | 持续集成Jenkins           |                                                        |        |
| 40    | 架构设计                  |                                                        |        |
|       |                           |                                                        |        |
|       |                           |                                                        |        |



## :baby:数据结构与算法

说明：以Java描述的常用数据结构与基本算法的操作，常见刷题笔记

- ### [概述](https://github.com/3218870799/-Note/blob/main/0.0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95.md#%E7%AC%AC%E4%B8%80%E7%AB%A0%E6%A6%82%E8%BF%B0)


- ### [数组](https://github.com/3218870799/-Note/blob/main/0.0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95.md#%E7%AC%AC%E4%BA%8C%E7%AB%A0%E6%95%B0%E7%BB%84)


- ### [队列](https://github.com/3218870799/-Note/blob/main/0.0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95.md#%E7%AC%AC%E4%B8%89%E7%AB%A0%E9%98%9F%E5%88%97)

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

- ### [一：基础语法](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#一基础语法)


- ### [二：常用API](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%BA%8C%E5%B8%B8%E7%94%A8api)


- ### [三：集合](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%B8%89%E9%9B%86%E5%90%88)

- ### [四：异常](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E5%9B%9B%E5%BC%82%E5%B8%B8)

- ### [五：多线程](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%BA%94%E5%A4%9A%E7%BA%BF%E7%A8%8B)

- ### [六：IO与NIO](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E5%85%ADio%E4%B8%8Enio)

- ### [七：网络编程](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%B8%83%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B)

- ### [八：JDBC](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E5%85%ABjdbc)

- ### [九：特性](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#%E4%B9%9D%E7%89%B9%E6%80%A7)

- ### [Java8](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java8)

- ### [Java9](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java9)

- ### [Java10](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java10)

- ### [Java11](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java11)

- ### [Java12](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java12)

- ### [Java13](https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md#java13)



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

### Dubbo

### SpringCloud

### 消息队列

### Zookeeper



## :3rd_place_medal:Linux



## :palm_tree:项目应用组件



### Lucene全文检索

### solr全文检索服务器

### shiro安全验证

### 业务流程管理Activiti

### 自动代码生成



## :shaved_ice:Netty

异步事件驱动的网络应用程序框架



## webservice

## :v:大数据

### Hadoop

###  HBase

###  Hive

### spark

## 网络安全





## :speak_no_evil:设计模式



## :snake:Python

## :m:机器学习  

##  

## SpringSession     

## 

## :mouse:Go   



## :package:项目管理



## 环境搭建

## VMware   

## 持续集成Jenkins

## 架构设计



# 我的博客

## 基于Redis的分布式锁



> > :cactus:已迁移整理至Github项目-Note，欢迎浏览提出建议

> >:dancer:[https://github.com/3218870799/-Note/blob/main/2.0.Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80/Java%E5%9F%BA%E7%A1%80.md](https://github.com/3218870799/-Note/blob/main/2.0.Java基础/Java基础/Java基础.md)

> >:athletic_shoe:建议下载使用Typora软件进行查看与编辑，效果更佳哦！



https://github.com/3218870799/-Note/blob/main/0.0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95.md

> > :haircut:**由于个人原因，工作后不如在学校的时间充裕，对于博客的排版等问题精力不足，**

> > **故将自己的学习笔记整理成Word文档和MD方式上传至GitHub，**

> > :dancing_women:**博客如果有什么新奇的想法依然还是会找时间更新的，毕竟园友还是很神通的都。**



> > :cake:**个人笔记项目地址：https://github.com/3218870799/-Note**