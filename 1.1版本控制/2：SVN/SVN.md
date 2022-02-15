

# 问题

svn“Previous operation has not finished; run 'cleanup' if it was interrupted“报错：

解决：

SVN内置了一个嵌入式DB SQLite，来保存SVN中管理的文件类型等；其` .svn ` 文件夹下有一个wc.db 可以使用sqlite打开 ，下载地址 ：https://www.sqlite.org/download.html ；

方案二：如果IDEA开发直接IDEA终端下运行

```shell
svn clean
```

