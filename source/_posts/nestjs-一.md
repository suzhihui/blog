---
title: nestjs(一)
date: 2020-08-30 22:54:42
tags: nestjs
category: 前端
cover: '/img/desk.png'
---
# 基础介绍
- 1、Nest基于TypeScript编写并且结合了 OOP(面向对象编程)，FP(函数式编程)和 FRP (函数式响应编程)的相关理念。在设计上的很多灵感来自于 Angular，Angular 的很多模 式又来自于 Java 中的 Spring 框架，依赖注入、面向切面编程等，所以我们也可以认为: Nest 是 Node.js 版的 Spring 框架
- 2、Nest 框架底层 HTTP 平台默认是基于 Express 实现的，所以无需担心第三方库的缺失。 Nest 旨在成为一个与平台无关的框架

## 为什么要使用脚手架
> 关于这个问题,其实我们在现代前端开发中，你使用vue,react等项目开发中,我们都会使用官方推荐的脚手架(所谓的xx-cli工具)来生成项目,这样的好处在于:
- 减少时间，不必从零开始搭建初始项目，提高开发效率。
- 便于多人协作
- 项目更新同步方便，只需要更新代码库中项目模板，即可下载最新的项目

# 脚手架的安装及常见的命令
## 一、[官网地址](https://docs.nestjs.com/cli/overview)

## `nest-cli`的基本使用

* 1、安装脚手架

  ```shell
  npm install -g @nestjs/cli
  ```

* 2、查看脚手架的版本

  ```shell
  nest -v
  ```

* 3、查看全部的命令(不记得使用命令创建文件的时候可以使用)
  > 根据下面打星多少来表示项目开中使用频率

  ```shell
  nest-book git:(master) nest   # 输入的命令
  Usage: nest <command> [options]

  Options:
    -v, --version                                   查看当前nestjs-cli的版本
    -h, --help                                      查看帮助

  Commands:
    new|n [options] [name]                          生成一个新的项目
    build [options] [app]                           构建一个项目
    start [options] [app]                           运行项目
    info|i                                          显示项目的具体信息
    update|u [options]                              升级之前的依赖包
    add [options] <library>                         将对外部库的支持添加到项目中。

    generate|g [options] <schematic> [name] [path]  Generate a Nest element.
      Available schematics:
        全称              别名(一般我们使用别名就可以)
        ┌───────────────┬─────────────┐
        │ name          │ alias       │
        │ application   │ application │
        │ class         │ cl          │
        │ configuration │ config      │
        │ controller    │ co          │            ** 控制器
        │ decorator     │ d           │            * 装饰器
        │ filter        │ f           │            * 过滤器
        │ gateway       │ ga          │            网关
        │ guard         │ gu          │            * 守卫
        │ interceptor   │ in          │            * 拦截器
        │ interface     │ interface   │            接口
        │ middleware    │ mi          │            中间层  
        │ module        │ mo          │            ** 模块
        │ pipe          │ pi          │            * 管道
        │ provider      │ pr          │
        │ resolver      │ r           │            graphql使用相当于上面的控制器
        │ service       │ s           │            ** 创建服务
        │ library       │ lib         │
        │ sub-app       │ app         │
        └───────────────┴─────────────┘
  ```

* 4、使用脚手架命令创建文件(控制器、服务层、模块)的时候会自动生成一个`spec`的测试文件,如果不想需要,可以加上参数

  ```shell
  nest g mo user [--no-spec]
  ```

* 5、创建一个`module`

  ```shell
  nest g mo user
  ```

* 6、创建一个控制器

  ```shell
  nest g co user
  nest g co user --no-spec # 创建不带测试文件的
  # 创建的会自动更新到user.module中(前提是你先创建了module)
  ```

* 7、创建一个服务层

  ```shell
  nest g s user 
  ```

  # 使用脚手架构建一个新的项目
  # <center>使用脚手架构建一个项目</center>

## 一、项目基本介绍

* 1、使用脚手架创建一个项目

  ```shell
  nest new nest-base
  ```
  
  > 选择安装依赖包的方式后就耐心等待
 
 ![输入图片说明](https://images.gitee.com/uploads/images/2020/0829/191742_a80102c9_1808543.png "image-20200713153707891.png")
  
* 2、初始化项目文件介绍(删除`node_module`后的项目)

  ```shell
  ➜  nest-base git:(master) tree
  .
  ├── README.md
  ├── nest-cli.json # nest-cli的配置
  ├── package-lock.json
  ├── package.json
  ├── src  # 项目文件
  │   ├── app.controller.spec.ts # 控制器的单元测试文件(可以删除)
  │   ├── app.controller.ts # 控制器文件(可以删除)
  │   ├── app.module.ts # 入口的module(不能删除)
  │   ├── app.service.ts # 服务层文件(可以删除)
  │   └── main.ts # 项目入口文件(不能删除)
  ├── test # 测试文件(可以删除)
  │   ├── app.e2e-spec.ts
  │   └── jest-e2e.json
  ├── tsconfig.build.json
  └── tsconfig.json # tsconfig的配置文件

  2 directories, 13 files
  ```


## 二、关于`nestjs`项目架构

* 1、官网提供的架构图

  ![输入图片说明](https://images.gitee.com/uploads/images/2020/0829/191807_440763ce_1808543.png "nestjs01.png")
  
  图中表述的意思:
  * 我们可以在项目中定义很多控制层
  * 客户端(浏览器、移动设备)发送`http`请求,根据`url`匹配的原则,到对应的控制器
  * 控制器继续访问到服务层
  * 服务层对接数据层,然后对数据的增删改查

* 2、实际中我们构建大型项目的时候的架构

  ![输入图片说明](https://images.gitee.com/uploads/images/2020/0829/191823_37b669a3_1808543.jpeg "nestjs02.jpg")

## 三、基础项目的解释

* 1、目录结构

  ```shell
  ➜  src git:(master) tree
  .
  ├── app.controller.spec.ts # 测试文件，可以先不看
  ├── app.controller.ts # 控制器
  ├── app.module.ts # 主模块
  ├── app.service.ts # 服务层
  └── main.ts # 入口文件

  0 directories, 5 files
  ```

* 2、`main.ts`文件

  ```typescript
  import { NestFactory } from '@nestjs/core';
  import { AppModule } from './app.module';
  
  async function bootstrap() {
    // 使用NestFactory工厂创建一个app应用并且传递一个AppModule模块进去,类似我们使用express框架一样的先创建一个app
    const app = await NestFactory.create(AppModule);
    // 监控端口,运行项目后浏览器直接访问localhost:3000
    await app.listen(3000);
  }
  bootstrap();
  ```

* 3、`app.module.ts`文件

  ```typescript
  import { Module } from '@nestjs/common';
  import { AppController } from './app.controller';
  import { AppService } from './app.service';

  @Module({
    imports: [], // 依赖外面的模块(可以是自己创建的比如userModule，或者是官方提供的比如typeorm, graphql,或者第三方的)
    controllers: [AppController], // 该模块所用到的控制器
    providers: [AppService], // 该模块的提供者
    exports: [], // 别的模块要使用该模块中的某几个方法，就要在这里对外暴漏
  })
  export class AppModule {}
  ```

* 4、`app.controller.ts`文件

  ```typescript
  import { Controller, Get } from '@nestjs/common';
  import { AppService } from './app.service';

  @Controller() // 使用nestjs的装饰器装饰表示该类是一个控制器
  export class AppController {
    constructor (
      // 使用依赖注入的方式注入一个类
      private readonly appService: AppService
    ) { }

    @Get() // 定义http的请求方式为get请求
    getHello(): string { // 函数名可以随便定义
      return this.appService.getHello(); // 控制层调用服务层的getHello()方法
    }
  }
  ```

* 5、`app.service.ts`文件

  ```typescript
  import { Injectable } from '@nestjs/common';

  @Injectable()
  export class AppService {
    getHello(): string {
      // 现在直接返回一个字符串,实际开发中这里调用typeorm中的方法对数据库进行curd操作
      return 'Hello World!';
    }
  }
  ```

  # 依赖注入的概念
  ## 一、依赖注入的概念

* 1、依赖注入的概念

  > 依赖注入(`DI`)是用来创建对象及其依赖的其它对象的一种方式。 当依赖注入系统创建某个对象实例时，会负责提供该对象所依赖的对象(称为该对象的依赖)。思想来源于`angular`

* 2、服务提供上`@Injectable`
  `@Injectable`装饰器会指出这些服务或其它类是用来注入的(比如一些工具方法)。它还能用于为这些服务提供配置项。


## 二、常见的定义提供器的方式【[代码见01.di分支](https://github.com/kuangshp/nest-book-code/tree/01.di/nest-base)】

* 1、基本方式(<font color="#f00"><b>也是最常见的</b></font>)

  ```typescript
  providers: [AppService]
  // 使用方式直接依赖注入就可以
  constructor (
    private readonly appService: AppService,
  ) { }
  ```

* 2、`uerClass`的方式

  ```typescript
  providers: [
    { 
      provide: AppService, 
      useClass: AppService 
    }
  ]
  // 使用方式直接依赖注入就可以
  constructor (
    private readonly appService: AppService,
  ) { }
  ```

* 3、`provide`定义字符串的方式

  ```typescript
  providers: [
    AppService,
    {
      provide: 'LOG',
      useClass: LoggerService
    }
  ],
  // 使用方式需要使用@Inject的方式
  constructor (
    private readonly appService: AppService,
    @Inject('LOG') readonly loggerService: LoggerService
  ) { }

  @Get()
  getHello(): string {
    this.loggerService.log('日志');
    return this.appService.getHello();
  }
  ```

* 3、使用`useFactory`工厂方式

  ```typescript
  @Module({
    imports: [],
    controllers: [AppController],
    providers: [
      {
        provide: 'LOG',
        useClass: LoggerService
      },
      {
        provide: 'APP_SERVICE',
        useFactory: (logger) => {
          logger.log('使用工厂方式');
          return '工厂方法返回';
        },
        inject: ['LOG'] // 注入的顺序就是useFactory函数的顺序
      }
    ],
    exports: []
  })

  // 使用方式
  @Controller()
  export class AppController {
    constructor (
      @Inject('APP_SERVICE') readonly appService: AppService,
      @Inject('LOG') readonly loggerService: LoggerService
    ) { }

    @Get()
    getHello(): string {
      this.loggerService.log('日志');
      console.log(this.appService);
      return 'nestjs';
    }
  }
  ```

* 4、使用`useValue`方式

  ```typescript
  providers: [
    ...
    {
      provide: 'IS_DEV',
      useValue: { isDev: true }
    }
  ],
  ...
  // 使用
  @Controller()
  export class AppController {
    constructor (
      @Inject('IS_DEV') readonly isDev: { isDev: boolean },
    ) { }

    @Get()
    getHello(): string {
      console.log(this.isDev);
      return 'nestjs';
    }
  }
  ```

# nestjs中模块的认识
## 一、[官网地址](https://docs.nestjs.com/modules)

## 二、关于`nestjs`中`module`的认识

* 1、在前面介绍到`nestjs`项目是由很多个`module`来组装成一个大项目的,类似现在前端框架是由很多的组件组装成一个项目。

* 2、来自官网介绍模块的图

  ![输入图片说明](https://images.gitee.com/uploads/images/2020/0829/192116_f79f4fb6_1808543.png "module.png")

  * 一个项目只能有一个根模块`app.module.ts`这个模块
  * 一个项目可以由多个模块组成
  * 模块之间可以相互的引入

* 3、模块的使用
  * 在`nestjs`中模块是使用`@Module`装饰器装饰的一个类
  * 在`@Module`中可接收一个对象，用来描素该模块的功能


  | 名称 | 描素 |
  | ---- | ---- |
  |`imports`|该模块依赖其它的模块, 以数组的方式，表示可以多个|
  |`controllers`|该模块在定义的控制器, 以数组的方式，表示可以多个|
  |`providers`|为该模块提供功能的提供者, 以数组的方式，表示可以多个|
  |`exports`|该模块需要对外暴漏出去给别的模板使用的功能, 以数组的方式，表示可以多个|


## 三、自定义业务模块

* 1、直接使用命令创建一个`module`

  ```shell
  # 在modules的文件夹下创建一个用户模块
  nest g mo modules/user
  ```

  ```shell
  # 创建后的提示语
  ➜  nest-base git:(02.module) ✗ nest g mo modules/user
  CREATE src/modules/user/user.module.ts (81 bytes)  # 表示创建的文件所在的位置
  UPDATE src/app.module.ts (317 bytes) # 更新了那个文件(会自动在app.module.ts的import中引入当前创建的module)
  ➜  nest-base git:(02.module) ✗ 
  ```

* 2、我们讲到可以在`module`中创建属于该`module`下面的控制器和服务层

  ```shell
  # 创建不带测试文件的控制器
  ➜  nest-base git:(02.module) ✗ nest g co modules/user --no-spec
  CREATE src/modules/user/user.controller.ts (97 bytes)
  UPDATE src/modules/user/user.module.ts (167 bytes)
  # 创建不带测试文件的服务层
  ➜  nest-base git:(02.module) ✗ nest g s modules/user --no-spec         
  CREATE src/modules/user/user.service.ts (88 bytes)
  UPDATE src/modules/user/user.module.ts (241 bytes)
  ➜  nest-base git:(02.module) ✗ 
  ```

* 3、上面我们说过`app.controller.ts`和`app.service.ts`是非必须文件,我们删除的时候要注意在`app.module.ts`中也要删除依赖引入

* 4、在`user.controller.ts`中添加一个访问用户列表的方法

  ```typescript
  import { Controller, Get } from '@nestjs/common';
  import { UserService } from './user.service';

  @Controller('user')
  export class UserController {
    constructor (
      private readonly userService: UserService,
    ) { }

    /**
     * @Get([path])当前的path会拼接到@Controller('user')到里面user的路径后面，不写就表示为空的
     */
    @Get()
    // userList这个方法名随便自己定义,要见文思意就可以
    async userList(): Promise<any[]> {
      // 控制层访问服务层的userList方法
      return await this.userService.userList();
    }
  }
  ```

* 5、在`user.service.ts`中定义与控制器对接的`userList`方法(模拟返回点数据)

  ```typescript
  import { Injectable } from '@nestjs/common';

  @Injectable()
  export class UserService {
    async userList(): Promise<any[]> {
      return [
        {
          id: 0,
          name: '张三'
        },
        {
          id: 1,
          name: '李四'
        }
      ]
    }
  }
  ```

* 6、运行项目

  ```shell
  npm run start:dev
  ```

* 7、浏览器上直接访问`localhost:3000/user`

## 四、自定义模块之间的相互引入

* 1、在上面的基础上创建一个日志模块及服务

  > 官方在`@nestjs/common`中提供了一个`Logger`日志模块,我们这仅仅是做演示模块之间的引入 

  ```shell
  ➜  nest-base git:(02.module) nest g mo modules/log          
  CREATE src/modules/log/log.module.ts (80 bytes)
  UPDATE src/app.module.ts (265 bytes)
  ➜  nest-base git:(02.module) ✗ nest g s modules/log --no-spec
  CREATE src/modules/log/log.service.ts (87 bytes)
  UPDATE src/modules/log/log.module.ts (151 bytes)
  ➜  nest-base git:(02.module) ✗ 
  ``` 

* 2、在`log.service.ts`中定义日志打印的方法

  ```typescript
  import { Injectable } from '@nestjs/common';

  @Injectable()
  export class LogService {

    log(str: string): void {
      console.log(str);
    }

    error(str: string): void {
      console.log(str);
    }
  }
  ```

* 3、我们要在`LogModule`模块以为的模块使用到上面定义的`log`和`error`方法就要在`LogModule`中对外暴露出去

  ```typescript
  import { Module } from '@nestjs/common';
  import { LogService } from './log.service';

  @Module({
    providers: [LogService],
    // 这里是以数组的方式,表示一个模块里面可以对外暴露多个
    exports: [LogService],
  })
  export class LogModule { }
  ```

* 4、在`app.module.ts`中删除`LogModule`的引入
* 5、在`user.module.ts`中引入`LogModule`模块

  ```typescript
  ...
  @Module({
    // 引入LogModule
    imports: [LogModule],
    controllers: [UserController],
    providers: [UserService]
  })
  export class UserModule { }
  ```
* 6、在`user.controller.ts`中使用`LogService`

  ```typescript
  @Controller('user')
  export class UserController {
    constructor (
      private readonly logService: LogService,
    ) { }

    @Get()
    async userList(): Promise<any[]> {
      // 控制层访问服务层的userList方法
      this.logService.log('运行了userList控制器');
      ...
    }
  }
  ```

* 7、<font color="#f00">注意点:如果要使用模块外的提供者而为引入模块会提示错误或者引入的模块中没有外暴露出来的提供者</font>
 
  ![输入图片说明](https://images.gitee.com/uploads/images/2020/0829/192027_4708ffc5_1808543.png "module01.png")

* 8、[代码参考](https://github.com/kuangshp/nest-book-code/tree/02.module)

## 五、全局模块
* 1、[参考文档](https://docs.nestjs.com/modules#global-modules)
* 2、全局模块的定义方式
> 在普通模块的上面添加一个`@Global()`的装饰器,对外暴露想暴露的提供者,就那么简单

* 3、为什么要使用全局模块
> 项目比较大,一些公共模块,会在项目很多个模块中需要引入的情况下

* 4、如果使用全局模块
  * 直接在`app.module.ts`中的`import`中添加全局模块
  * 在需要使用全局模块中暴露的提供者,不需要在该模块中`import`
  * 直接依赖注入就可以
* 5、[代码参考地址](https://github.com/kuangshp/nest-book-code/tree/03.global_module)

## 六、动态模块
* 1、官网地址[地址一](https://docs.nestjs.com/modules#dynamic-modules)和[地址二](https://docs.nestjs.com/fundamentals/dynamic-modules)

* 2、使用动态模块的场景
> 需要向模块中传递参数的情况下，我们就要使用动态模块,不传递参数就使用普通模块即可

* 3、动态模块中常见的2个静态方法,选择用其中一个就可以
  * `register()`
  * `forRoot()`

#### 使用`register`静态方法创建一个日志的动态模块

 * 1、定义模块

  ```typescript
  import { Module, DynamicModule } from '@nestjs/common';
  import { LogService } from './log.service';

  @Module({})
  export class LogModule {
    // register函数中可以接收外部传递过来的变量,或者对象
    static register(prefix: string): DynamicModule {
      return {
        module: LogModule,
        providers: [
          LogService,
          // 使用useValue的方式在模块中注入一个变量,可以理解为在该模块中注入了别的模块,只是注入的方式不是采用import
          // 而是采用模块调用静态方法的方式
          {
            provide: 'PREFIX',
            useValue: prefix
          }
        ],
        // 动态模块一样的也要对外暴露出去
        exports: [LogService]
      }
    }
  }
  ```

* 2、在日志中使用当前模块提供的变量

  ```typescript
  import { Injectable, Inject } from '@nestjs/common';

  @Injectable()
  export class LogService {
    constructor (
      @Inject('PREFIX') private readonly prefix: string, // 依赖注入的方式
    ) { }

    log(str: string): void {
      console.log(`${this.prefix}-${str}`);
    }
  }
  ```
* 3、在`user.module.ts`使用动态模块

  ```typescript
  @Module({
    imports: [
      LogModule.register('user')
    ],
    controllers: [UserController]
  })
  export class UserModule { }
  ```

* 4、控制器上使用和之前一样的使用

  ```typescript
  @Controller('user')
  export class UserController {
    constructor (
      private readonly logService: LogService,
    ) { }

    @Get()
    hello(): string {
      this.logService.log('hello的控制器');
      return 'hello word';
    }
  }
  ```

* 5、[参考代码](https://github.com/kuangshp/nest-book-code/tree/04.dynamic_module/nest-base)

#### 使用`forRoot`静态方法的方式创建一个日志模块

* 1、使用方式和`register`一样的
* 2、两者之间的区别[网上也没具体介绍](https://stackoverflow.com/questions/59358741/are-there-any-differences-between-using-forroot-or-register-when-creating-a)
* 3、[参考代码](https://github.com/kuangshp/nest-book-code/tree/04.dynamic_module_02/nest-base)

## 七、解决模块之间的循环依赖
* 1、[官方文档](https://docs.nestjs.com/fundamentals/circular-dependency#module-forward-reference)
* 2、比较常见的场景
> 用户模块和授权模块,登录用户登录了，我们要给该用户授权(就依赖了授权模块),授权模块又要根据用户信息来授权(依赖了用户模块),这样就造成一个循环依赖的关系

* 3、创建用户模块和授权模块,并且创建对应的服务与暴露出去
* 4、在用户的控制器中注入授权的服务
* 5、在授权的服务层注入用户的服务
* 6、直接提示错误,并且提示你错误解决参考地址
  ![输入图片说明](https://images.gitee.com/uploads/images/2020/0829/192049_cbee883e_1808543.png "module02.png")
* 7、我们在相互引入模块的地方采用

  ```typescript
  imports: [forwardRef(() => AuthModule)],
  ```
* 8、[参考代码](https://github.com/kuangshp/nest-book-code/tree/05.module/nest-base)

## 八、[官方提供的`module`](https://github.com/nestjs)使用方式见各自文档


# <center>nestjs生命周期钩子函数(<font color="#f00">使用频率低</font>)</center>

## 一、[官网地址](https://docs.nestjs.com/fundamentals/lifecycle-events)

## 二、常见的生命周期

所有应用程序元素都有一个由`Nest`管理的生命周期。`Nest`提供了生命周期钩子，提供了对关键生命时刻的可见性，以及在关键时刻发生时采取行动的能力。

* 1、常见的生命周期钩子函数

| 名称                          | 描素                                            |
| ---- | ----|
| `onModuleInit()`              | 初始化主模块后调用                              |
| `onApplicationBootstrap()`    | 在应用程序完全启动并引导后调用                  |
| `onModuleDestroy()`           | 在Nest销毁主模块(`app.close()`方法之前进行清理) |
| `beforeApplicationShutdown()` | 响应系统信号(当应用程序关闭时，例如`SIGTERM`)   |

* 2、使用方式

  ```typescript
  import { Injectable, OnModuleInit } from '@nestjs/common';

  @Injectable()
  export class UsersService implements OnModuleInit {
    onModuleInit() {
      console.log(`The module has been initialized.`);
    }
  }
  ```