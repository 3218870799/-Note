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

# 二：插件

插件网址：https://www.npmjs.com/；进入后搜索gitbook即可

1：自动生成目录插件

不过所有的文件名都得改成README.md





# 三：使用

## SUMMARY

使用基本连接即可；

限定为三级



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

新建分支保存html

