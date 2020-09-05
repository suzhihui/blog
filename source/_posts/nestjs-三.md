---
title: nestjs(三)
date: 2020-09-03 22:26:14
tags: nestjs
category: 前端
cover: '/img/desk.png'
---

## 一、什么是`TypeOrm`
`TypeORM`是一个优秀的 `Node.js ORM`框架，采用`TypeScript`编写，支持使用`TypeScript` 或 `Javascript（ES5，ES6，ES7）`开发。目标是保持支持最新的`Javascript`特性来帮助开发各种用户数据库的应用 - 不管是轻应用还是企业级的。

简单一句话概括就是使用`typescript`来写的对象关系映射(`Object Relational Mapping`,简称`ORM,或O/RM,或O/R mapping`)

## 二、`TypeORM`能做什么

* 根据模型自动创建数据库表
* 可以透明的插入/更新/删除数据库对象
* 映射数据库`table`到`Javascript`对象，映射表列到`Javascript`对象属性
* 提供表的一对一，多对一，一对多，多对多关系处理

## 三、我们为什么要使用`TypeORM`
* `TypeORM`可以帮助开发者专注于业务逻辑，而不用过于担心数据存储的问题。
* 我们不需要特意去学习各种种`SQL`语句
* 不需要考虑`SQL`注入等一些因素
* 不需要考虑未来更换数据库来修改`SQL`语句

## 四、目前`Nodejs`中常用的`ORM`框架

* [`TypeORM`](https://typeorm.io/)
* [`TypeORM中文文档`](https://www.bookstack.cn/read/TypeORM-0.2.20-zh/spilt.17.spilt.3.README.md)
* [`sequelize`](https://sequelize.org/)

在`Nestjs`中两个`ORM`都支持使用,一般使用`TypeORM`的比较多


## 一、环境搭建

* 1、全局安装包

  ```shell
  npm install typeorm -g
  ```

* 2、创建一个项目并且指定数据库

  ```shell
  typeorm init --name projectName --database mysql
  ```
  * --name指定项目名称
  * --database指定选用那种数据库,目前支持的数据库有`mysql, mariadb, postgres, sqlite, mssql, oracle, mongodb,cordova, react-native, expo, nativescript`(前提是你的电脑上安装的对应的数据库)

* 3、初始化项目后的目录结构

  ```shell
  .
  ├── README.md
  ├── ormconfig.json
  ├── package.json
  ├── src
  │   ├── entity
  │   │   └── User.ts
  │   ├── index.ts
  │   └── migration
  └── tsconfig.json

  3 directories, 6 files
  ```
  * `ormconfig.json`文件配置与数据库连接相关的信息
  * `src/entity`表示实体类,一般约定为建表的对象(默认创建一个User.ts)的文件
  * `src/index.ts`入口文件,练习对数据的增删改查操作

* 4、安装依赖包
* 5、修改`ormconfig.json`文件数据库配置信息
* 6、运行命令并且查看数据库是否自动创建表(<font color="#f00">需要手动创建一个数据库</font>)

  ```shell
  npm run start
  ```

* 7、<font color="#f00">**注意点:**</font>
  * `mysql`中编码选择,要选用`utf8mb4`编码才是`utf8`,不是选`utf8`就表示是`utf8`编码
  * `utf8mb4_bin`字符集排序规则

## 二、`ormconfig.json`文件详解

* 1、解析各个字段

  ```json
  {
    "type": "mysql", // 选用的数据库
    "host": "localhost", // 数据库地址
    "port": 3306, // 数据库端口
    "username": "test", // 数据库用户名
    "password": "test", // 数据库密码
    "database": "test", // 数据库
    "synchronize": true, // 是否同步true表示会自动将src/entity里面定义的数据模块同步到数据库生成数据表(已经存在的表的时候再运行会报错)
    "dropSchema": true, // 删除数据库中的表
    "logging": false, // 是否打印日志,执行sql语句时候输出原生sql,也可以配置成一个数组["query", "error", "schema"]指定sql的执行类型
    "charset": "utf8mb4", // 编码
    "timezone": "local", // 时区,默认本地,也可以写"+8"
    "entityPrefix": "", // 给此数据库连接上的所有表（或集合）加的前缀。
    "entities": [ // 定义TypeORM需要查找的数据模型的,可以定义多个
        "src/entity/**/*.ts"
    ],
    "migrations": [ // 数据迁移文件生成的地方
        "src/migration/**/*.ts"
    ],
    "subscribers": [ // 订阅(用的少)
        "src/subscriber/**/*.ts"
    ],
    "cli": { // 数据迁移工具使用的
        "entitiesDir": "src/entity",
        "migrationsDir": "src/migration",
        "subscribersDir": "src/subscriber"
    }
  }
  ```

## 三、关于`ormconfig.json`文件的几种写法
* 1、直接选择默认的`ormconfig.json`,`src/index.ts`中的`createConnection`方法会默认去读取
* 2、直接使用`ormconfig.js`类似上面`ormconfig.json`一样的会默认读取

  ```js
  module.exports = {
    type: 'mysql',
    host: 'localhost',
    port: 3306,
    username: 'root',
    password: '123456',
    database: 'typeorm_mysql',
    synchronize: true,
    logging: true,
    dropSchema: true,
    entities: [
      'src/entity/**/*.ts'
    ]
  }
  ```
* 3、还可以使用`ormconfig.xml`和`ormconfig.yml`格式
* 4、[以上配置参考代码](https://github.com/kuangshp/nest-book-code/tree/10.type_orm/typeorm_mysql)
* 5、不使用配置文件,直接在`src/index.ts`的`createConnection`方法中写死(可以直接删除`ormconfig.[format]`的文件, `format=[js，ts，json，yml，yaml，xml]`)

  ```typescript
  createConnection({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: '123456',
      database: 'typeorm_mysql',
      synchronize: true,
      logging: true,
      dropSchema: true,
      entities: [
          'src/entity/**/*.ts'
      ]
  }).then(async connection => {
    // 业务代码
  })
  ```
* 6、连接远程数据库(比如可能买了阿里云数据库服务的业务)

  ```typescript
  createConnection({
    type: "mysql",
    url: "mysql://test:test@localhost/test"
  });
  ```

* 7、一般选用`ormconfig.js`就可以主要出于3方面原因
  * 1、查询顺序`[js，ts，json，yml，yaml，xml]`从左到右边开始的顺序查找
  * 2、如果存在多个数据库的时候直接导出一个数组
  * 3、可以使用`.env`的文件,避免数据库敏感信息泄漏



前面我们介绍了`TypeORM`是一个对象关系数据模型的工具,我们要对数据库的增删改查等操作先要创建表,建表我们不直接写原生`SQL`语句(但是创建数据库还是要我们手动创建的),我们直接写`typescript`的对象,通过同步的方式就自动创建对应对象的表,一个实体类就生成一张数据表

## 一、实体类的认识

* 1、最基本的结构

  ```typescript
  import { Entity, PrimaryGeneratedColumn, Column, CreateDateColumn, UpdateDateColumn } from "typeorm";

  // Entity是由@Entity装饰器装饰的模型。将为此类模型创建数据库表
  @Entity()
  export class User {
    // 下面定义的会自动在数据库中生成对应的字段
    // 主键且自动自增的
    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    name: string;

    @Column()
    password: string;

    @CreateDateColumn()
    createdAt: Date;

    @UpdateDateColumn()
    updateAt: Date;
  }
  ```

* 3、关于`@Entity()`中可扩充的参数

  可配置的参数
  * `name`配置映射到数据库表名,如果不指定会以实体类名字创建数据表
  * `orderBy`排序方式(一般不用)
  * `engine`引擎(一般不用)
  * `database`数据库(一般不用,除非多个数据库的时候)

* 4、关于字段装饰器参数的介绍
  不管是使用`@PrimaryGeneratedColumn()`、`@Column()`等装饰器装饰字段的我们都叫字段装饰器

  可配置的参数
  * `type`配置字段类型,在`mysql`中字符类型可能是`char`、`varchar`、`text`,数字类型可能是`int`、`tinyint`,小数类型可能是`float`、`double`、`decimal(10,2)`等
  * `name`真正映射到`mysql`数据库中字段名字,如果不指定会默认以对象的字段为名字(<font color="#f00">建议都指定</font>)
  * `length`长度,比如在`mysql`中字段为`varchar`的时候指定字段长度
  * `nullable`在`mysql`中字段是否可以为`NULL`值,默认为`false`
  * `select`改字段是否可以被查询出来(针对使用`typeORM`的查寻操作,不针对你使用原生`SQL`语句操作),默认为`true`表示可以被查询出来
  * `default`默认值,比如插入数据的时候,没传递该字段的值,就默认一个值
  * `unique`是否唯一约束
  * `comment`备注该字段是做什么的(<font color="#f00">建议都写上,方便阅读</font>)
  * `enum`枚举类型
  * `array`该列是否以数组

* 5、一份完整的实体类清单

  ```typescript
  import { Entity, PrimaryGeneratedColumn, Column, CreateDateColumn, UpdateDateColumn } from "typeorm";

  @Entity({ name: 'user' })
  export class User {
    @PrimaryGeneratedColumn({
      type: 'int',
      name: 'id',
      comment: '主键id'
    })
    id: number;

    @Column({
      type: 'varchar',
      nullable: false,
      length: 50,
      unique: true,
      name: 'username', // 如果是一样的可以不指定
      comment: '用户名'
    })
    username: string;

    @Column({
      type: 'varchar',
      nullable: false,
      length: 100,
      comment: '密码'
    })
    password: string;

    @Column('tinyint', {
      nullable: false,
      default: () => 0,
      name: 'is_del',
      comment: '是否删除,1表示删除,0表示正常'
    })
    isDel: number;

    @CreateDateColumn({
      type: 'timestamp',
      nullable: false,
      name: 'created_at', // mysql数据库规范是使用下划线命名的,不使用驼峰
      comment: '创建时间'
    })
    createdAt: Date;

    @UpdateDateColumn({
      type: 'timestamp',
      nullable: false,
      name: 'updated_at',
      comment: '更新时间',
    })
    updateAt: Date;
  }
  ```

* 6、执行命令`npm run start`数据表会自动同步到数据库中
* 7、生成的数据结构

  ```sql
  mysql> desc user;
  +------------+--------------+------+-----+----------------------+-------------------+
  | Field      | Type         | Null | Key | Default              | Extra             |
  +------------+--------------+------+-----+----------------------+-------------------+
  | id         | int          | NO   | PRI | NULL                 | auto_increment    |
  | username   | varchar(50)  | NO   | UNI | NULL                 |                   |
  | password   | varchar(100) | NO   |     | NULL                 |                   |
  | is_del     | tinyint      | NO   |     | 0                    |                   |
  | created_at | timestamp(6) | NO   |     | CURRENT_TIMESTAMP(6) | DEFAULT_GENERATED |
  | updated_at | timestamp(6) | NO   |     | CURRENT_TIMESTAMP(6) | DEFAULT_GENERATED |
  +------------+--------------+------+-----+----------------------+-------------------+
  6 rows in set (0.01 sec)

  mysql> 
  ```


## 一、修改下配置

<font color="#f00">**注意点:**</font>前面一章节我们已经自动生成表了,我们需要将`ormconfig.js`文件里面的几个选项改`false`,不然会直接删除数据库中的表,或者报错,当前不想要该表的时候开启

  ```js
  module.exports = {
    ...
    synchronize: false,
    dropSchema: false,
    ...
  }
  ```

## 二、新增数据

* 1、新增数据

  ```ts
  import "reflect-metadata";
  import { createConnection } from "typeorm";
  // 引入刚刚定义的实体类
  import { User } from "./entity/User";

  createConnection().then(async connection => {
    const user = new User()
    user.username = '张三';
    user.password = '123456';
    // save里面传递一个对象
    connection.manager.save(user).then(user => {
      console.log('插入成功', user);
    });
  }).catch(error => console.log(error));
  ```

* 2、使用`async/await`方式新增数据

  ```ts
  createConnection().then(async connection => {
    // 2.使用async+await
    const user = new User();
    user.username = '李四';
    user.password = '123456';
    const result = await connection.manager.save(user);
    console.log('插入结果', result);
  }).catch(error => console.log(error));
  ```

* 3、使用`Repositories`(使用句柄的方式)方式新增数据

  ```ts
  createConnection().then(async connection => {
    // 3.使用Repositories方式新增数据
    const user = new User();
    user.username = '王五';
    user.password = '123456';
    const userRepository = connection.getRepository(User);
    const result = await userRepository.save(user);
    console.log(result);
  }).catch(error => console.log(error));
  ```
* 4、查询数据表中数据

  ```sql
  mysql> select * from user;
  +----+----------+----------+--------+----------------------------+----------------------------+
  | id | username | password | is_del | created_at                 | updated_at                 |
  +----+----------+----------+--------+----------------------------+----------------------------+
  |  1 | 张三     | 123456   |      0 | 2020-07-29 14:18:27.138086 | 2020-07-29 14:18:27.138086 |
  |  2 | 李四     | 123456   |      0 | 2020-07-29 14:46:02.196676 | 2020-07-29 14:46:02.196676 |
  |  3 | 王五     | 123456   |      0 | 2020-07-29 14:51:29.918676 | 2020-07-29 14:51:29.918676 |
  +----+----------+----------+--------+----------------------------+----------------------------+
  3 rows in set (0.00 sec)

  mysql> 
  ```

## 三、删除数据
* 1、删除数据比较简单,直接指定要删除的数据就可以(先要查询出来然后删除)

  ```ts
  createConnection().then(async connection => {
    // 4.删除数据
    // 4.1创建一个句柄
    const userRepository = connection.getRepository(User);
    // 4.2根据句柄去查询实体findOne传递数字会默认根据id查询
    const user = await userRepository.findOne(1);
    // 4.3删除数据
    await userRepository.remove(user);
  }).catch(error => console.log(error));
  ```

## 四、修改数据

* 1、修改数据也是先查询出来,然后修改后，保存到数据库中

  ```ts
  // 5.修改数据
  const userRepository = connection.getRepository(User);
  const user = await userRepository.findOne(2);
  user.password = '23456';
  await userRepository.save(user);
  ```