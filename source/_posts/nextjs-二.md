---
title: nextjs(二)
date: 2020-09-01 20:59:08
tags: nestjs
category: 前端
cover: '/img/desk.png'
---

# <center>`RESTful API`的设计指南</center>
## 一、[参考文档](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

## 二、常见的请求方式

* 1、请求方式对比

|请求方式|说明|
|----|----|
|`GET`请求|从服务器取出资源（一项或多项）|
|`POST`请求|在服务器新建一个资源|
|`PUT`请求|在服务器更新资源（客户端提供改变后的完整资源）|
|`PATCH`请求|在服务器更新资源（客户端提供改变的属性）|
|`DELETE`请求|从服务器删除资源|

* 2、区分`PUT`和`PATCH`
  * 1、两个请求方式都是向服务器发送修改数据的请求
  * 2、`PUT`可以理解为彻底全部的更新数据
  * 3、`PATCH`可以理解为打补丁,就是更新数据的一部分<font color="#f00">(也是我们比较常用的方式)</font>

## 三、设计接口规范(对阮一峰文档的补充)
> 认真看下阮一峰文章里面对日常`RESTful API`开发,会对其了解,我相信也能写出符合标准的`RESTful API`接口,但是我还是要补充一点

* 1、对于增删改查严格使用`GET`、`POST`、`PATCH`、`DELETE`的请求方式,不要清一色的使用`GET`和`POST`请求
* 2、上面不管是`GET`、`POST`、`PATCH`、`DELETE`都表示一个动作(所谓的动词)
* 3、我们在设计`API`的时候就不要在出现**动词**了,比如`xxx/api/v1/get_user`

  ```typescript
  xxx/api/v1/get_user // 不好的接口设计 get请求 在url中使用了get这个动词
  xxx/api/v1/save // 不好的接口设计 post请求 提交数据,在url中出现动词save

  xxx/api/v1/user //get请求 表示获取用户列表,返回是一个数组
  xxx/api/v1/user/1 //get请求 表示获取用户信息,返回一个对象
  ```

* 4、一般我们的`url`地址都是使用下划线命名的方式,不使用小驼峰命名方式

  ```typescript
  xxx/api/v1/modifyPassword // 不要使用小驼峰命名,url选用下划线的方式
  ```

  ## 一、服务器端接收客户端传递的参数的几种方式
* 1、`POST`、`PATCH`请求体中发送的数据(最常见)
* 2、`GET`请求的`query`参数方式
  ```typescript
  xxx/api/v1/user?pageSize=1
  ```
* 3、`GET`、`PATCH`、`DELETE`请求的`params`的参数

  ```typescript
  xxx/api/v1/user/1 // 获取用户id=1的数据
  ```
* 4、请求头的数据,常见的`token`

## 二、在`Nestjs`中接收浏览器上的参数

* 1、常见的方法列表

| No.  | 名字                    | 字段说明(参考`express`框架字段)                   |
| :--: | ----------------------- | ------------------------------------------------- |
|  1   | `@Request()`              | `req` 获取到req请求的参数                           |
|  2   | `@Response()`             | `res` 使用了res就不使用使用return返回值需要使用res.send()  |
|  3   | `@Next()`                 | next                                              |
|  4   | `@Session()`              | `req.`session                                       |
|  5   | `@Param(key?: string)`    | `req.params` / `req.params[key]` 获取动态路由的参数   |
|  6   | `@Body(key?: string)`     | `req.body` / `req.body[key]` 获取post请求提交的参数   |
|  7   | `@Query(key?: string)`    | `req.query` / `req.query[key]` 获取get请求query的参数 |
|  8   | `@Headers(name?: string)` | `req.headers` / `req.headers[name]` 获取请求头的参数  |


* 2、关于`@Query()`获取全部的参数

  ```typescript
  @Controller('user')
  export class UserController {
    // 批量获取全部的参数,接收到的是一个对象,你传递什么我就接收什么
    @Get()
    userList(
      @Query() query: any
    ): string {
      console.log(query);
      return '用户列表';
    }
  }
  // 浏览器访问的url地址:http://localhost:3000/user?name=hello&age=20
  ```

* 3、选择性接收`Query()`中带参数并且判断参数类型

  ```typescript
  @Controller('user')
  export class UserController {
    // 只接收全部参数里面的其中一个或者多个,ParseIntPipe是nestjs中内置管道
    @Get()
    userList(
      @Query('age', new ParseIntPipe()) age: number,
      @Query('name') name: string
    ): string {
      // 我只要age和name字段,别的你传递多的给我，我也不接收也不处理
      console.log(age, name);
      return '用户列表'
    }
  }
  // 浏览器访问的url地址:http://localhost:3000/user?name=hello&age=20
  ```

* 4、`@Param`参数的获取

  ```typescript
  @Get(":id")
  userInfo(
    @Param() params:any
  ) {
    console.log(params); // 输出{ id: '2' }
    return "用户详情"
  }
  // 浏览器访问的url地址:http://localhost:3000/user/2
  ```

* 5、`@Param`单独接受参数

  ```typescript
  @Get(":id")
  userInfo(
    @Param('id', new ParseIntPipe()) id: number
  ) {
    console.log(id);
    return "用户详情"
  }
  // 浏览器访问的url地址:http://localhost:3000/user/2
  ```

* 6、`@Body()`接受`post`提交过来的数据(一次性接收全部的,也可以在`@Body()`中加参数类似上面的方式一样的校验传递过来的参数[仅仅是针对参数比较少的情况下])

  ```typescript
  @Post()
  addUser(
    @Body() body: any
  ) {
    // 这种写法适合大规模的提交参数,自己又不想一个一个去校验
    console.log(body);
    return body
  }
  // 使用postman提交post请求地址:http://localhost:3000/user/
  ```
* 7、[参考代码](https://github.com/kuangshp/nest-book-code/tree/06.params)

## 三、注意点(<font color="#f00">个人建议</font>)
我们在写控制层和服务层的时候劲量遵循增删改查的顺序来写,这样方便以后查找代码比较方便,方便直接定位到哪里

### 一、关于前端模板引擎的介绍

现在如果是面向多端开发的模式下,会进行前后端分离式的方式进行开发,一般用`nestjs`直接返回`api`接口,前端用`angular`、`react`、`vue`去做的,如果不考虑做多端开发,快速点可以直接在`nestjs`写前端模板,类似我们比较传统的`java`开发中,使用`jsp`模板来渲染前端页面。根据项目具体情况选择该技术栈


## 二、在`Nestjs`中自己配置`ejs`模板

* 1、[官网地址](https://docs.nestjs.com/techniques/mvc)

* 2、安装模板引擎包

  ```shell
  npm install ejs --save
  ```

* 3、在`main.ts`中配置

  ```typescript
  import 'dotenv/config';
  import { NestFactory } from '@nestjs/core';
  import { AppModule } from './app.module';
  import { Logger } from '@nestjs/common';
  // 引入包
  import { NestExpressApplication } from '@nestjs/platform-express';
  import { join } from 'path';
  
  const PORT = process.env.PORT || 8080;
  
  async function bootstrap() {
    const app = await NestFactory.create<NestExpressApplication>(AppModule);
    // 配置静态文件的目录
    //方式一是直接访问:localhost:4000/1.jpg
    //app.useStaticAssets(join(__dirname, '..', 'public')); 
    //方式二是访问:localhost:4000/static/1.jpg但是在public文件夹下不需要创建static目录
    app.useStaticAssets(join(__dirname, '..', 'public'), {
    		prefix: '/static/'
    });
    // 配置视图文件的目录
    app.setBaseViewsDir(join(__dirname, '..', 'views')); 
    app.setViewEngine('ejs');
    await app.listen(PORT, () => {
      Logger.log(`服务已经启动,请访问:http://wwww.localhost:${PORT}`);
    });
  }
  bootstrap();
  
  ```

* 3、在项目的根目录下创建`public`和`views`的目录

* 4、测试`views`文件夹下创建一个`index.ejs`文件

  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>

  <body>
    <h1>测试页面</h1>
  </body>

  </html>
  ```

* 5、控制器中使用`@Render`渲染模板

  ```typescript
  import { Controller, Get, Render } from '@nestjs/common';

  @Controller()
  export class AppController {
    @Get()
    @Render('index')
    getHello(): any {
      return { name: '哈哈' };
    }
  }
  ```

* 6、在浏览器上输入`localhost:3000`查看结果

## 三、测试访问静态目录文件

* 1、在`public`中创建`css`、`js`、`images`文件夹
* 2、在`index.ejs`模板中使用样式、`js`、图片

  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link href="/static/css/index.css" rel="stylesheet" />
  </head>

  <body>

    <h1>测试页面</h1>
    <img src="/static/images/dog.jpg" />
  </body>
  <script src="/static/js/jquery.min.js"></script>
  <script>
    $(function () {
      $('h1').on('click', function () {
        alert('点击了我');
      })
    })
  </script>

  </html>
  ```

## 四、模板数据渲染
在远古时代的前端开发或者叫页面仔的时代，前端开发把静态页面写好了，发给后端的同学(java,php)他们使用模板不同的模板引擎，在前端静态页面中把需要显示从数据库获取的数据的地方使用特殊的符合，动态的显示数据。

* 1、被处理的前端模板,等待后端数据反填上去

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title><%=title%></title>
  </head>
  
  <body>
    <h1><%=title%></h1>
    <form action="/user/login" method="post">
      <p>
        <input type="text" name="username" />
      </p>
      <p>
        <input type="password" name="password" />
      </p>
      <p>
        <button type="submit">提交</button>
      </p>
    </form>
  </body>
  
  </html>
  ```

* 2、在`Nestjs`中渲染前端模板及返回数据

  ```typescript
  import { Controller, Get, Render, Post, Body, Response } from '@nestjs/common';
  
  @Controller('user')
  export class UserController {
    @Get()
    index() {
      return '主页';
    }
  
    @Get('login')
    @Render('login') // 渲染views里面的ejs模板
    loginPage() {
      // 这里的数据到时候讲到数据库，服务层的时候直接从数据库拉取数据,现在先写个假数据
      return { 'title': '登录页面' } // 返回给ejs模板的数据
    }
  
    @Post('login')
    login(@Body() body, @Response() res) {
      console.log(body); // 获取表单中提交的数据
      res.redirect('/user'); // 重定向到用户首页
      // 注意如果在控制器函数中使用了@Response()就不能使用return返回值
    }
  }
  ```

* 3、在浏览器上直接访问`localhost:3000/user/login`
* 4、[本章节代码](https://github.com/kuangshp/nest-book-code/tree/07.ejs)



## 一、`Cookie`的介绍

* 1、`HTTP` 是无状态协议。简单地说，当你浏览了一个页面,然后转到同一个网站的另一个页 面，服务器无法认识到这是同一个浏览器在访问同一个网站。每一次的访问，都是没有任何关系的。如果我们要实现多个页面之间共享数据的话我们就可以使用 `Cookie` 或者 `Session` 实 现
* 2、`cookie` 是存储于访问者的计算机中的变量。可以让我们用同一个浏览器访问同一个域名的时候共享数据。

## 二、`Cookie`的特点

  * `cookie` 保存在浏览器本地
  * 正常设置的 `cookie` 是不加密的，用户可以自由看到;
  * 用户可以删除 `cookie`，或者禁用它
  * `cookie` 可以被篡改
  * `cookie` 可以用于攻击
  * `cookie` 存储量很小。实际上要被`localStorage`替代，但是后者 IE9 兼容。

## 三、在`Nestjs`中使用`Cookie`

* 1、[`cookie-parser`地址](https://www.npmjs.com/package/cookie-parser)

* 2、下载

  ```shell
  npm install cookie-parser
  npm install @types/cookie-parser -D
  ```

* 3、在`main.ts`中使用

  ```typescript
  // 导包
  import * as cookieParser from 'cookie-parser';
  
  async function bootstrap() {
    const app = await NestFactory.create(AppModule);
    // 使用
    app.use(cookieParser());
    await app.listen(3000);
  }
  bootstrap();
  ```

* 4、在组件的控制器中写入和获取`cookie`

  ```typescript
  import { Controller, Get, Response, Request } from '@nestjs/common';
  
  @Controller('user')
  export class UserController {
    @Get()
    index(@Request() req) {
      console.log(req.cookies.name, '当前的cookie');
      return '主页';
    }
  
    @Get('login')
    login(@Response() res) {
      // 如果使使用了res就不能使用return，必须使用send
      res.cookie('name', 'hello', { maxAge: 1000 * 5, httpOnly: true });
      res.send('登录页面');
    }
  }
  ```

* 5、在浏览器控制图查看`cookie`

  > 到了时间刷新浏览器控制图上的刷新的,`cookie`就会被清空的

  ![输入图片说明](https://images.gitee.com/uploads/images/2020/0831/201855_084c9f0f_1808543.jpeg "nestjs03_cookie.jpg")
  



* 6、关于`cookie`的参数说明

| NO.  | 参数       | 类型      | 参数说明                                                     |
| :--: | :--------- | --------- | ------------------------------------------------------------ |
|  1   | `domain`   | `String`  | 指定域名下有效                                               |
|  2   | `expires`  | `Date`    | 过期时间(秒),设置在某个时间点后会在该`cookoe`后失效          |
|  3   | `httpOnly` | `Boolean` | 默认为`false`表示不允许客户端(通过`js`来获取`cookie`)        |
|  4   | `maxAge`   | `String`  | 最大失效时间(毫秒),设置在多少时间后失效                      |
|  5   | `path`     | `String`  | 表示`cookie`影响到的路径,如:`path=/`如果路径不能匹配的时候,浏览器则不发送这个`cookie` |
|  6   | `secure`   | `Boolean` | 当 `secure` 值为` true` 时,`cookie` 在 `HTTP` 中是无效,在 `HTTPS `中才有效 |
|  7   | `signed`   | `Boolean` | 表示是否签名`cookie`,如果设置为`true`的时候表示对这个`cookie`签名了,这样就需要用`res.signedCookies()`获取值`cookie`不是使用`res.cookies()`了, |



* 7、签名`cookie`的设置

  ```typescript
  // main.ts中
  app.use(cookieParser(process.env.SECRET)); // 配合dotenv包来使用
  ```

  ```typescript
  import { Controller, Get, Response, Request } from '@nestjs/common';
  
  @Controller('user')
  export class UserController {
    @Get()
    index(@Request() req) {
      console.log(req.signedCookies, '当前的cookie');
      return '主页';
    }
  
    @Get('login')
    login(@Response() res) {
      // 如果使使用了res就不能使用return，必须使用send
      // res.cookie('name', 'hello', { maxAge: 1000 * 5, httpOnly: true });
      res.cookie('name', 'hello', { maxAge: 1000 * 5, httpOnly: true, signed: true })
      res.send('登录页面');
    }
  }
  ```

* 8、如果要销毁`cookie`常见的方式
  * 直接将值设置为空
  * 设置`maxAge=0`

* 9、[本小节代码](https://github.com/kuangshp/nest-book-code/tree/08.cookie)

## 四、`Nestjs中使用session`

* 1、**`session`** 是另一种记录客户状态的机制，不同的是 **`Cookie`** 保存在客户端浏览器中，而 **`session`** 保存 在服务器上

* 2、`session`的工作流程
  当浏览器访问服务器并发送第一次请求的时候,服务器端会创建一个`session`对象,生成一个类似于`{key: '', value: ''}`的对象,然后将`key(cookie)`返回到浏览器(客户)端,浏览器再次访问的时候携带这个`key(cookie)`到服务器端,找到这个`session`的`value`值。

* 3、在`nestjs`中使用`express-session`进行`session`的操作

  ```typescript
  npm install express-session
  npm install @types/express-session -D
  ```
* 4、在`main.ts`中配置`session`

  ```typescript
  import { NestFactory } from '@nestjs/core';
  import { AppModule } from './app.module';
  import * as session from 'express-session';

  async function bootstrap() {
    const app = await NestFactory.create(AppModule);
    // 配置中间件使用session,加盐是123456(随便写的)
    app.use(session({ secret: '123456', cookie: { maxAge: 60000 } }))
    await app.listen(3000);
  }
  bootstrap();
  ```
* 5、创建`user`的模块及控制器(忽视请求方式,只是方便浏览器模拟请求)

  ```typescript
  import { Controller, Get, Request, Response } from '@nestjs/common';

  @Controller('user')
  export class UserController {
    @Get()
    index(
      @Request() req: { [key: string]: any }
    ): string {
      console.log(req.session);
      return '用户主页';
    }

    @Get('login')
    login(
      @Response() res: { [key: string]: any },
      @Request() req: { [key: string]: any }
    ): void {
      req.session.name = 'hello';
      // 再次提醒使用了@Response就不能使用return
      res.send('登录页面')
    }
  }
  ```

* 6、先在浏览器中访问`localhost:3000/user/login`然后访问`localhost:3000/user`

  ```typescript
  // 编辑器控制图打印出信息,可以获取到刚刚设置的session
  Session {
    cookie: {
      path: '/',
      _expires: 2020-07-27T09:28:20.046Z,
      originalMaxAge: 60000,
      httpOnly: true
    },
    name: 'hello'
  }
  ```

* 7、常见参数的说明

| NO   | 参数               | 说明                                                         |
| ---- | ------------------ | ------------------------------------------------------------ |
| 1    | `secret`           | 一个 `String` 类型的字符串，作为服务器端生成` session` 的签名 |
| 2    | `name`             | 返回客户端的`key` 的名称，默认为`connect.sid`,也可以自己设置 |
| 3    | `resave`           | 强制保存 `session `即使它并没有变化,。默认为` true`。建议设置成 `false` |
| 4    | `saveUninitalized` | 强制将未初始化的 `session` 存储。当新建了一个 `session` 且未设定属性或值时，它就处于 未初始化状态。在设定一个 `cookie` 前，这对于登陆验证，减轻服务端存储压力，权限控制是有帮助的。( 默认:`true`)。建议手动添加 |
| 5    | `cookie`           | 设置返回到前端`key` 的属性，默认值为`{ path: ‘/’, httpOnly: true, secure: false, maxAge: null }`。 |
| 6    | `rolling`          | 在每次请求时强行设置 `cookie`，这将重置` cookie` 过期时间(默认:`false`) |

* 8、`session`的销毁

* 设置`maxAge=0`

  ```typescript
  req.session.cookie.maxAge=0
  ```

* 将值设置为空

* 使用`destroy`销毁方法

  ```typescript
  req.session.destroy((err) => {
    ...
  })
  ```
* 9、[本小节代码地址](https://github.com/kuangshp/nest-book-code/tree/09.session)