# 一：安装

优先确定安装的node.js版本，有的版本不支持gitBook，我已开始选择的是最新版的，会报错误

```shell
cb.apply is not a function
```

我选择的是 版本[node-v10.21.0-x64](https://nodejs.org/dist/v10.21.0/node-v10.21.0-x64.msi)

切换淘宝镜像

```shell
# 查看当前镜像
npm config get registry

# 切换镜像
npm config set registry https://registry.npm.taobao.org

```

安装

```shell
npm install -g gitbook-cli

gitbook -V
```

初始化：切换到自己的工作目录下，执行下列命令，这个过程可能有点久；

```shell
gitbook init
```

启动服务，访问http://localhost:4000

```shell
gitbook serve
```

如果想要将静态网页内容输出到你想要的目录

```shell
gitbook build --output=/tmp/gitbook
```

输出为PDF，需要先安装gitbook pdf

```shell
npm install gitbook-pdf -g
```

执行

```shell
gitbook pdf 
```



# 二：插件

插件网址：https://www.npmjs.com/；进入后搜索gitbook即可

在目录下的book.json文件中添加配置

执行下列命令安装插件

```shell
gitbook install
```

重新启动

```shell
gitbook serve
```





1：自动生成目录插件

不过所有的文件名都得改成README.md



2：评论插件

```json
"plugins": [
    "disqus"
],
"pluginsConfig": {
    "disqus": {
        "shortName": "gitbook-tutorial"
    }
}
```



# 三：使用

## SUMMARY

使用基本连接即可；

限定为三级

自动生成

方法一：

安装

```shell
npm install -g gitbook-summary
```

使用

```shell
cd /path/to/your/book/
book sm
```







## 忽略文件

和Git一样，会依次读取`.gitignore`, `.bookignore` 和 `.ignore` 文件来将一些文件和目录排除。

## 配置文件

book.json

## 封面

gitbook 的封面可以通过插件[auto cover](https://plugins.gitbook.com/plugin/autocover)自动生成，也可以自己配置。

如果要使用自定义的封面，在书籍的根目录下放置 `cover.jpg`，如果想要缩略图可以放置 `cover_small.jpg`，文件格式必须为 jpg。

一个好的封面需要:

- 大小要求 cover.jpg 1800x2360 pixels , cover_small.jpg 200x262
- 不要有边框
- 有清晰的标题
- 任何小的标题需要清晰可见



# 四：部署到GitHub

源码分支将新产生的部分编译文件添加到忽略目录并提交

新建分支` gh-pages ` 保存编译后的html

```shell
git checkout -b gh-pages
```

将原项目所有删除，将_book文件夹中文件拷贝出来提交到gh-pages分支；

Github切换到gh-pages分支，然后打开GitPages的链接即可；

提交master分支自动更新的工具gh-pages分支的工具；

切换分支

```shell
# 查看所有分支
git branch -a
# 切换分支
git checkout 分支名
```



我的GitBook笔记地址：https://3218870799.github.io/-Note/

