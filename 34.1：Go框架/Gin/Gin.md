liwenzhou.com

# 一：Gin基本使用

新建Go module (vgo)

返回JSON格式的数据，多端都可以一起用；减少返回的内容；



中间件：

go语言通过首字母大小写分辨是否对外可见；大写对外可见，小写不可见；

## 渲染

模板语言

## 路由

其实就是Controller层

路由组：将拥有共同URL前缀的路由划分为一个路由组，习惯上用一堆{}包裹同组路由，路由组同样支持嵌套

```go
r := gin.Default()
shopGroup := r.Group("/shop")
	{
		shopGroup.GET("/index", func(c *gin.Context) {...})
		shopGroup.GET("/cart", func(c *gin.Context) {...})
		shopGroup.POST("/checkout", func(c *gin.Context) {...})
		// 嵌套路由组
		xx := shopGroup.Group("xx")
		xx.GET("/oo", func(c *gin.Context) {...})
	}
r.run()
```

## 中间件

就是Java中的AOP切面，允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数，适合处理一些公共的业务逻辑，比如登录认证……

Gin的中间件必须是一个` gin-HandlerFunc ` 类型

```go
//统计耗时请求
func StatCost() gin.HandlerFunc{
    return func(c *gin.Context){
        start:= time.Now()
        //可以通过c.set在请求上下文中设置值，后续的处理函能够取到这个值
        c.Set("name","xqc")
        //调用原函数
        c.Next()
        //不调用剩余的处理程序
        //c.Abort()
        cost:= time.Since(start)
        log.Println(cost)
    }
}
```

中间件既可以全局使用，又可以单个使用

```go
//全局路由使用
func main(){
    r:=gin.New()
    r.Use(StatCost)
    ……
}
//单个路由使用
r.GET("/test2", StatCost(),方法名)
//为路由组注册
shopGroup := r.Group("/shop", StatCost())
{
}
```



# 二：GORM使用

对象关系映射，不过这样对牺牲执行性能，牺牲灵活性，弱化SQL能力；

安装

```shell
go get -u github.com/jinzhu/gorm
```

连接mysql数据库，执行简单增删改查操作

```go
package main

import (
	"fmt"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

// UserInfo 用户信息
type UserInfo struct {
	ID uint
	Name string
	Gender string
	Hobby string
}


func main() {
	db, err := gorm.Open("mysql", "root:root1234@(127.0.0.1:13306)/db1?charset=utf8mb4&parseTime=True&loc=Local")
	if err!= nil{
		panic(err)
	}
	defer db.Close()

	// 自动迁移
	db.AutoMigrate(&UserInfo{})

	u1 := UserInfo{1, "七米", "男", "篮球"}
	u2 := UserInfo{2, "沙河娜扎", "女", "足球"}
	// 创建记录
	db.Create(&u1)
	db.Create(&u2)
	// 查询
	var u = new(UserInfo)
	db.First(u)
	fmt.Printf("%#v\n", u)

	var uu UserInfo
	db.Find(&uu, "hobby=?", "足球")
	fmt.Printf("%#v\n", uu)

	// 更新
	db.Model(&u).Update("hobby", "双色球")
	// 删除
	db.Delete(&u)
}
```

其他复杂的查询有点像MybatisPlus的语法，可以参考官网；

## 结构体标记(tags)

