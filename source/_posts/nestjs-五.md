---
title: nestjs(五)
date: 2020-09-09 22:56:16
tags: nestjs
category: 前端
---



前面的章节中我们仅仅是讲解了`TypeORM`的使用,如何将`TypeORM`在`Nestjs`中使用将在本章节讲解


## 一、配置步骤
* 1、[官网地址](https://docs.nestjs.com/techniques/database)
* 2、安装依赖包

  ```shell
  npm install --save @nestjs/typeorm typeorm mysql
  ```

* 3、创建一个`user`的模块

* 4、直接在`app.module.ts`中配置数据库的文件

  ```ts
  import { TypeOrmModule } from '@nestjs/typeorm';

  @Module({
    imports: [
      TypeOrmModule.forRoot({
        
        type: 'mysql',
        host: 'localhost',
        port: 3306,
        username: 'root',
        password: '123456',
        database: 'typeorm_mysql',
        synchronize: true, // 自动同步数据模型到数据表中
        logging: false,
        entities: [
          __dirname + '/**/*.entity{.ts,.js}'
        ],
      }),
      UserModule,
    ],
  })
  export class AppModule {}
  ```

* 5、在`user`的模块下创建一个`user.entity.ts`的实体类(可以直接复制上一章节的代码过来)
* 6、**<font color="#f00">最重要的一点,自己创建的实体类要对应的模块中加载进来</font>**

  ```ts
  import { Module } from '@nestjs/common';
  import { TypeOrmModule } from '@nestjs/typeorm';
  import { UserEntity } from './user.entity';

  @Module({
    imports: [
      TypeOrmModule.forFeature([ // 这步骤是不能少的
        UserEntity,
      ])
    ]
  })
  export class UserModule { }
  ```

## 二、结合控制层和服务层测试将数据插入到数据库并且取出数据

* 1、创建控制器和服务层

  ```shell
  # 创建控制层
  nest g co modules/user --no-spec
  # 创建服务层
  nest g s modules/user --no-spec 
  ```

* 2、在服务层中写一个新增数据和查询数据的方法

  ```ts
  import { Injectable } from '@nestjs/common';
  import { InjectRepository } from '@nestjs/typeorm';
  import { Repository } from 'typeorm';

  import { UserEntity } from './user.entity';

  @Injectable()
  export class UserService {
    constructor (
      @InjectRepository(UserEntity)
      private readonly userRepository: Repository<UserEntity>
    ) { }

    // 创建数据,传递一个对象类型的数据
    async createUser(data: { [propName: string]: any }): Promise<UserEntity> {
      return await this.userRepository.save(data);
    }

    // 查询全部的数据
    async userList(): Promise<UserEntity[]> {
      return await this.userRepository.find();
    }
  }
  ```

* 3、控制层中用来接收客户端的请求,对接服务层

  ```ts
  @Controller('user')
  export class UserController {
    constructor (
      private readonly userService: UserService,
    ) { }

    @Post()
    async createUser(
      @Body() data: { [propName: string]: any }
    ): Promise<UserEntity> {
      return await this.userService.createUser(data);
    }

    @Get()
    async userList(): Promise<UserEntity[]> {
      return await this.userService.userList();
    }
  }
  ```
* 4、使用`postman`测试接口
* 5、[本章节的代码](https://github.com/kuangshp/nest-book-code/tree/18.nest_mysql)

## 三、配合`.env`文件及数据迁移脚本来更改数据库表结构

> 官网上也有相关的例子:配置`ormconfig.json`到根目录下,这个在之前的版本上我是配置成功了,在现在的版本上似乎有点小问题。本人也折腾了好久,感觉下面介绍的方法还是比较靠谱的，只要基本配置就可以。

* 1、安装依赖包并且在`main.ts`中引入

  ```shell
  npm install dotenv
  npm install @types/dotenv -D
  ```

* 2、安装获取配置文件的依赖包,[官网上讲的是这个包](https://docs.nestjs.com/techniques/configuration)

  ```shell
  npm install nestjs-config
  ```

* 3、在根目录下创建一个`.env`的文件存放数据库连接的基本信息

  ```shell
  DB_TYPE=mysql
  DB_HOST=localhost
  DB_USERNAME=root
  DB_PASSWORD=123456
  DB_DATABASE=typeorm_mysql
  DB_PORT=3306
  DB_LOGGING=true
  ```

* 4、在根目录下创建一个`ormconfig.js`的文件(仅仅是用来做数据迁移的)

  ```js
  module.exports = [
    {
      name: 'default',
      type: process.env.DB_TYPE,
      host: process.env.DB_HOST,
      port: Number(process.env.DB_PORT),
      database: process.env.DB_DATABASE,
      username: process.env.DB_USERNAME,
      password: process.env.DB_PASSWORD,
      logging: false,
      entities: [
        'src/entity/**/*.entity.{ts,js}',
        'src/modules/**/*.entity.{ts,js}'
      ],
      migrations: [
        'src/migration/*.ts'
      ],
      subscribers: [
        'src/subscriber/**/*.ts'
      ],
      cli: {
        'entitiesDir': 'src/entity',
        'migrationsDir': 'src/migration',
        'subscribersDir': 'src/subscriber'
      }
    }
  ]
  ```

* 5、根目录下创建一个`config/database.config.ts`的文件

  ```ts
  export default {
    type: process.env.DB_TYPE,
    host: process.env.DB_HOST,
    port: Number(process.env.DB_PORT),
    database: process.env.DB_DATABASE,
    username: process.env.DB_USERNAME,
    password: process.env.DB_PASSWORD,
    logging: true,
  };
  ```

* 6、在`app.module.ts`的文件中使用`nestjs-config`来加载数据库的配置

  ```ts
  import { ConfigModule, ConfigService } from 'nestjs-config';
  import { TypeOrmModule } from '@nestjs/typeorm';

  @Module({
    imports: [
      // 配置加载配置文件
      ConfigModule.load(path.resolve(__dirname, 'config', '**/!(*.d).{ts,js}'), {
        modifyConfigName: name => name.replace('.config', ''),
      }),
      // mysql的连接
      TypeOrmModule.forRootAsync({
        useFactory: async (config: ConfigService) => ({
          type: config.get('database.type'),
          host: config.get('database.host'),
          port: config.get('database.port'),
          username: config.get('database.username'),
          password: config.get('database.password'),
          database: config.get('database.database'),
          entities: [__dirname + '/**/*.entity{.ts,.js}'],
          logging: config.get('database.logging'),
        }),
        inject: [ConfigService],
      }),
    ],
    controllers: [],
    providers: [],
  })
  export class AppModule { }
  ```

* 7、在`package.json`文件中配置数据库迁移脚本(你可以记得住也不可以配置的)

  ```json
  "scripts": {
    "generate": "ts-node ./node_modules/.bin/typeorm migration:generate -n Test",
    "db": "ts-node ./node_modules/typeorm/cli.js migration:run"
  }
  ```

* 8、将`.env`文件添加到`.gitignore`文件中,不提交到`git`上面
* 9、当有实体类变动的时候就运行第七点的两个脚本,自动修改数据表结构,而不会造成数据的丢失
* 10、[本章节的代码](https://github.com/kuangshp/nest-book-code/tree/19.nest_mysql)

## 一、数据库中事务操作的必要性

在数据库操作中,如果涉及到多表关联、多表有关系操作的时候,我们就要进行事务处理,以免一张表的数据已经发生了修改,刚好发生了异常,造成另外一张表的数据不进行更新。

常见场景

- 两张表关联关系同时需要插入、删除数据
- 银行转换一个表数据减少另外一个表的数据增加

## 二、方式一、隐式`commit`,隐式`rollback`

- 1、控制层的代码

  ```js
  @Post()
  async create(@Body() data: Extract<CreateUserDto, CreateUserExtendDto>) {
    return this.userService.create(data);
  }
  ```

- 2、服务层的代码

  ```js
  async create(data: Extract<CreateUserDto, CreateUserExtendDto>) {
    const { name, password, email, mobile, gender, qq, address } = data;
    return getManager()
      .transaction(async (entityManage: EntityManager) => {
        const user: { [propName: string]: any } = await entityManage.save(
          UserEntity,
          {
            name,
            password,
            email,
            mobile,
            gender,
          },
        );
        //throw new Error('主动抛出错误');
        await entityManage.save(UserExtendEntity, {
          userId: user.id,
          qq,
          address,
        });
      })
      .then(res => {
        console.log(res);
        return '创建成功';
      })
      .catch(e => {
        console.log(e);
        return '创建失败';
      });
  }
  ```

## 三、显式的使用`commit`和`rollback`

- 1、控制层的代码

  ```js
  @Post()
  async create(@Body() data: Extract<CreateUserDto, CreateUserExtendDto>) {
    return this.userService.create(data);
  }
  ```

- 2、服务层的使用

  ```js
  async create(data: Extract<CreateUserDto, CreateUserExtendDto>) {
    const { name, password, email, mobile, gender, qq, address } = data;
    const connection = getConnection();
    const queryRunner = connection.createQueryRunner();
    await queryRunner.connect();
    await queryRunner.startTransaction();
    try {
      // 注意如果这个地方使用save是不行的
      const user = await queryRunner.manager.insert<UserEntity>(UserEntity, {
        name,
        password,
        email,
        mobile,
        gender,
      });
      Logger.log(JSON.stringify(user), '插入user的数据');
      const userId = user.identifiers[0].id;
      await queryRunner.manager.insert<UserExtendEntity>(UserExtendEntity, {
        userId,
        qq,
        address,
      });
      await await queryRunner.commitTransaction();
      return '创建成功';
    } catch (e) {
      await queryRunner.rollbackTransaction();
      throw new BadRequestException('创建失败');
    }
  }
  ```

## 四、方式三、在控制层中使用装饰器的方式(这种方式和第一种有点类似)

* 1、控制层的代码

  ```js
  @Post()
  @Transaction()
  async create(
    @Body() data: Extract<CreateUserDto, CreateUserExtendDto>,
    @TransactionManager() manager: EntityManager,
  ) {
    return this.userService.create(data, manager);
  }
  ```

* 2、在服务层中

  ```js
  async create(
    data: Extract<CreateUserDto, CreateUserExtendDto>,
    manager: EntityManager,
  ) {
    const { name, password, email, mobile, gender, qq, address } = data;
    const user: { [propName: string]: any } = await manager.save(UserEntity, {
      name,
      password,
      email,
      mobile,
      gender,
    });
    Logger.log(JSON.stringify(user), '当前用户');
    // throw new Error('错误了') // 测试主动抛出异常是否回滚
    await manager.save(UserExtendEntity, {
      userId: user.id,
      qq,
      address,
    });
    return '创建成功';
  }
  ```