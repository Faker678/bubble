## 🎫 预览

![img](https://gitee.com/Faker777/image/raw/master/typora/22.png)

## [#](https://www.golangroadmap.com/project/bubble.html#简介)📑 简介



![image-20220514200521573](https://gitee.com/Faker777/image/raw/master/typora/image-20220514200521573.png)



这是一个前后端分离的小实验项目，代码总量在120行左右，前端文件是在别处下载下来的，适合学完go语言基础后进一步学习gin框架的入门选手学习。在自行学习过go基础语法、mysql简单操作以及gin、gorm等基础知识过后可上手实践。源代码中注释比较详细，如果能自主实现这个项目，相信对gin框架就能有一个大体上的把握，对接着学习更加深入的gin框架知识会有所帮助。

小清单项目的功能类似于记事本，具体归纳为以下：

- **1.增加待办事项**
- **2.删除待办事项**
- **3.修改待办事项状态**
- **4.查看所有待办事项**

当看到以上四点之后你就会发现，这不就是数据库的**增删改查**么？没错，就是这样，这个项目归根结底就是在对数据库进行增删改查的操作，然后通过前端页面返回一个结果，简而言之，其实大部分的项目都是在对数据库进行一系列的操作，然后通过一定途径返回给前端。

当然，我们用的是gorm，这就省去了一条又一条的数据库语句，如果你对数据库的知识已经忘的差不多了，没关系。gorm会根据你调用的方法自动生成数据库语句去和数据库进行交互。

- gitee地址 ：https://gitee.com/Faker777/bubble.git

  

## [#](https://www.golangroadmap.com/project/bubble.html#项目的总概)📖 项目的总概：

第一步：创建数据库

第二步：搭架子

第三步：连接数据库<记得延迟关闭数据库连接（defer）

第四步：绑定模型（数据迁移）

<完善部分>

告诉gin框架去哪里找模版文件 告诉gin框架模版文件引用的静态文件去哪里找

第五步：分析小清单项目业务逻辑，写好逻辑注释

- 添加一个待办事项(前端页面填写待办事项，点击提交，会发请求到这里)

```go
v1Group.POST("/todo",func(c *Context){
	1.<从请求中把数据拿出来>
	2.<存入数据库>
	3.<返回响应>
	POST
})
```

- 查看所有待办事项

```go
v1Group.GET("/todo",func(c *Context){

})
	1.<获取todo表中的所有数据>
	2.<定义一个切片存放查询到的数据>
	3.<查询成功则返回该切片>
	GET
```

- 修改某个待办事项

```go
v1Group.PUT("/todo/:id",func(c *Context){
	1.<从请求中把id拿出来>
	2.<在数据库中根据id把数据查询出来>
	3.<将表todo与前端的操作绑定在一起>
	4.<根据前端的操作修改查询到的数据，并保存>
	PUT
})
```

- 删除某个待办事项

```go
v1Group.DELETE("/todo/:id",func(c *Context){
	1.<从请求中将id拿出来>
	2.<在表中根据id将数据删除>
	DELETE
})
```

最后根据所写逻辑注释，去gin框架和gorm的官方操作文档中，查询需要用到的方法以及该方法如何使用，这样，你就能实现小清单项目，顺带将gin和gorm的操作方法熟悉一边，有助于熟练掌握gin+gorm这种模式的使用，日后多做几个小项目，你就成长啦！



## [#](https://www.golangroadmap.com/project/bubble.html#快速开始)⚡️ 快速开始：

### [#](https://www.golangroadmap.com/project/bubble.html#基础准备)🚀 基础准备

首先将项目`git`在本地

```go
go git clone https://gitee.com/Faker777/bubble.git
```

由于我们的小清单需要用到gin框架和gorm，因此需要在本地安装这两个框架先

```go
go get -u github.com/gin-gonic/gin
```

```go
go get -u github.com/jinzhu/gorm
```

### [#](https://www.golangroadmap.com/project/bubble.html#🗄️-配置数据库连接)🗄️ 配置数据库连接

开启mysql数据库服务,并建库memorandum（不需要创建表，代码会自动生成）

将`:`将initMysql()方法中的数据库连接串中的`root:rootroot`改成你的数据库用户名和密码，此时就完成了部署。

```go
"root:123456t@tcp(127.0.0.1:3306)/bubble?&parseTime=True&loc=Local" 
//我把uft8mb删掉了，否则会报错
```

### [#](https://www.golangroadmap.com/project/bubble.html#启动项目)⭐️ 启动项目

进入项目根目录（main.go所在路径），执行 `go run main.go` 。启动后，浏览器访问：`http://localhost:8080/`

## [#](https://www.golangroadmap.com/project/bubble.html#实现过程)⚔️ 实现过程

以下是代码实现过程，以及过程中实现出现的问题，供大家参考。

- 第一步：搭建好架子

```go
packge main
import(
  "github.com/gin-gonic/gin"
	"github.com/jinzhu/gorm"
)
func main(){
  r := gin.Default()
  r.GET("/",func(c *Context){
    c.HTML(http.StatusOK,"index.html",nil)
  })
  r.Run(":8080")
}
```

复制代码

- 第二步：写好数据库连接方法和连接数据库

```go
func initMysql() (err error) {
	dsn := "root:123456t@tcp(127.0.0.1:3306)/bubble?&parseTime=True&loc=Local" //我把uft8mb删掉了，否则会报错
	DB, err = gorm.Open("mysql", dsn)
	if err != nil {
		panic(err)
	}
	return DB.DB().Ping() //返回一个ping()方法测试链接的结果
}
```

复制代码

- 第三步：连接数据库

```go
func main() {
	err := initMysql()
	if err != nil {
		panic(err)
	}
	defer DB.Close()//注意记得延时关闭
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.html", nil)
	})
```

复制代码

### [#](https://www.golangroadmap.com/project/bubble.html#突发情况)💥 突发情况

此时我们打开127.0.0.1:8080会发现，啥都没有，那是因为系统不知道去哪里找模版文件，好，那咱就告诉它上哪找模版文件

```go
//告诉gin框架去哪里找模版文件
	r.LoadHTMLFiles("templates/index.html") //这里根据情况而定，需要自己调试一下这个路径
```

此时我们再刷新一下，发现还是有问题，那是因为系统不知道上哪找模版文件引用的静态文件，那咱也告诉它就是了：

```go
//告诉gin框架模版文件引用的静态文件在哪里
	r.Static("/static", "static")
//这里的前一个是相对路径，表示浏览器要服务器提供的文件，而第二个根目录则是指服务器需要提供的文件放在哪里
```

加上这两个之后就会发现，好家伙的，有画面了！

接下来就可以开心的一边看`gin`和`gorm`官方文档一边写业务代码了呀。

## [#](https://www.golangroadmap.com/project/bubble.html#待完善部分)💌 待完善部分

该项目中有些功能并不完善，比如只有`查看所有待办事项`而没有`查看某个待办事项`，只有`删除某个待办事项`而没有`删除所有待办事项`，如果有同学希望能够完整的掌握`gorm`和数据库操作的话，建议将之进一步完善，这样就能更进一步地提高你的水平啦。

