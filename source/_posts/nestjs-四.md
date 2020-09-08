---
title: nestjs(四)
date: 2020-09-08 21:31:11
tags: nestjs
category: 前端
---

前面我们仅仅介绍了`TypeORM`对数据的增、删、改操作,一直未提查询数据的方法,用也仅仅是用了`find`和`findOne`的基本使用方式,因为我们在实际开发中80%的对数据库操作都是在查询数据,以下将重点介绍查询数据

## 一、`find`的基本使用
* 1、全部查询所有的字段

  ```ts
  // 1.查询全部的字段
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find();
  console.log(result);
  ```
  
  执行的`SQL`类似

  ```sql
  select * from user;
  ```

* 2、使用`select`选择性的查询想要的字段

  ```ts
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({ select: ['id', 'username'] });
  console.log(result);
  ```

  执行的`SQL`类似 

  ```sql
  select id, username from user;
  ```

* 3、使用`where`条件查询

  ```ts
  // 3.使用where条件查询
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({ where: { id: 1 } });
  console.log(result);
  ```

  执行的`SQL`类似 

  ```sql
  select * from user where user.id = 1;
  ```

* 4、使用`where`多条件`and`的关系查询数据

  ```ts
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({ where: { id: 1, username: 'xx' } });
  console.log(result);
  ```

  执行的`SQL`类似 

  ```sql
  select * from user where user.id = 1 and user.username = 'xx';
  ```

* 5、使用`where`多条件`or`的关系查询数据

  ```ts
  // 5.使用where..or查询数据
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({ where: [{ id: 1 }, { username: 'xx' }] });
  console.log(result);
  ```

  执行的`SQL`类似 

  ```sql
  select * from user where user.id = 1 or user.username = 'xx';
  ```

* 6、`relations`关系查询(<font color="#f00">前提是要先有外键关联关系</font>)

  ```ts
  // 6.relations关系查询
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({ relations: ['userDetail'] });
  console.log(result);
  ```

  执行的`SQL`类似 

  ```sql
  select a.*,b.* from user as a left join user_extend as b on a.id = b.userId;
  ```

* 7、使用`join`关系查询(对`relations`的扩展)

  ```ts
  // 7.使用join
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({
      join: {
          alias: 'user',
          leftJoinAndSelect: {
              detail: 'user.userDetail',
              posts: 'user.posts'
          }
      }
  });
  console.log(JSON.stringify(result));
  ```

* 8、`order`排序查询

  ```ts
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({
      order: {
          id: 'DESC',
          username: 'ASC'
      }
  });
  console.log(result);
  ```

  执行的`SQL`类似 

  ```sql
  select * from user order by username asc, id desc;
  ```

* 9、分页查询数据
  * `skip`偏移(表示从哪里开始)
  * `take`查询多少条数据

  ```ts
  // 9.分页查询
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({
      skip: 0,
      take: 10,
  })
  console.log(result);
  ```

  执行的`SQL`类似 

  ```sql
  select * from user limit 0,10;
  ```

## 二、`Find`的进阶选项

`TypeORM`提供了许多内置运算符，可用于创建更复杂的查询：

* 1、`Not`

  ```ts
  // 10.Not
  const userRepository = connection.getRepository(User);
  const result = await userRepository.find({
      username: Not('王五')
  });
  console.log(result);
  ```

  执行的`SQL`类似 

  ```sql
  select * from user where username != '王五';
  ```

* 2、`LessThan`小于,一般用于数字
* 3、`LessThanOrEqual`小于或者等于
* 4、`MoreThan`大于
* 5、`MoreThanOrEqual`大于等于
* 6、`Equal`等于
* 7、`Like`模糊查询 `xx: LIKE('%yy%')`
* 8、`Between`两个范围之间`xx: Between(1,10)`
* 9、`In`在什么里面`xx: In(['',''])`
* 10、上面的可以多个组合起来一起使用

## 三、`TypeORM`不仅仅提供`Find`方法,还有一系列的方法

* 1、`find`查找返回一个数组
* 2、`findOne`查询返回一个对象,直接传递一个数字进去,会根据`id`去查询
* 3、`findAndCount`查询返回数量`[data, count]`
* 4、`findByIds([])`根据`id`数组查询

## 四、[本章节代码](https://github.com/kuangshp/nest-book-code/tree/15.find)

## 一、什么是`QueryBuilder`

`QueryBuilder`是`TypeORM`最强大的功能之一 ,它允许你使用优雅便捷的语法构建`SQL`查询，执行并获得自动转换的实体。也是进行复杂查询必使用的技能之一。

## 二、创建`QueryBuilder`的几种方式

* 1、使用`connection`创建方式一

  ```ts
  import "reflect-metadata";
  import { createConnection, getConnection } from "typeorm";
  import { User } from "./entity/User";

  createConnection().then(async connection => {
    // 1.使用connection创建
    const user = await getConnection()
      .createQueryBuilder()
      .select(['user.id', 'user.username']) // 需要选择查询的字段,如果想要全部查询可以不加select
      .from(User, 'user') // 从哪张表,并且定义别名为user
      .where('(user.id=:id)', { id: 1 }) // 过滤条件
      .getOne(); // 查询一个
    console.log(user);
  }).catch(error => console.log(error));
  ```

* 2、使用`connection`创建方式二

  ```ts
  // 2.使用connection创建
  const user = await getConnection()
    .createQueryBuilder(User, 'user')
    .select(['user.id', 'user.username'])
    .where('(user.id=:id)', { id: 1 })
    .getOne();
  console.log(user);
  ```

* 3、使用`entity manager`

  ```ts
  // 3.使用entity manager创建
  const user = await getManager()
    .createQueryBuilder(User, 'user')
    .select('user')
    .getMany();
  console.log(user);
  ```

* 4、使用`repository`

  ```ts
  // 4.使用repository创建
  const user = await getRepository(User)
    .createQueryBuilder('user')
    .getMany();
  console.log(user);
  ```

## 三、`QueryBuilder`五种类型
* 1、查询数据

  ```ts
  const user = await getConnection()
    .createQueryBuilder(User, 'user')
    .select(['user.id', 'user.username'])
    .where('(user.id=:id)', { id: 1 })
    .getOne();
  console.log(user);
  ```

* 2、插入数据

  ```ts
  // 插入数据
  const result = await getConnection()
    .createQueryBuilder()
    .insert() // 插入数据的时候要指明插入到那个实体类
    .into(User)
    .values([{ username: '张三', password: '1234' }, { username: '李四', password: '12345' }])
    .execute();
  console.log(result);
  ```

* 3、更新数据

  ```ts
  // 更新数据
  const result = await getConnection()
    .createQueryBuilder()
    .update(User)
    .set({ username: '哈哈哈' })
    .where('id=:id', { id: 1 })
    .execute();
  console.log(result);
  ```

* 4、删除数据

  ```ts
  // 删除数据
  const result = await getConnection()
    .createQueryBuilder()
    .delete()
    .from(User)
    .where('id=:id', { id: 3 })
    .execute();
  console.log(result);
  ```
* 5、创建关系查询

  ```ts
  // 创建关系查询
  const result = await getConnection()
    .createQueryBuilder(User, 'user')
    // 第一个参数是定义字段,第二个实体类,第三个是别名,第四个是条件
    .leftJoinAndMapMany('user.posts', Posts, 'posts', 'user.id=posts.userId')
    .getMany();
  console.log(JSON.stringify(result));
  ```

## 四、查询数据方法

* 1、`getOne`查询一条数据
* 2、`getMany`查询多条数据
* 3、`getRawOne`使用聚合函数的时候下查询一条数据
* 4、`getRawMany`使用聚合函数的时候下查询多条数据

  ```ts
  const result = await getConnection()
    .createQueryBuilder(User, 'user')
    // 使用了聚合函数就要使用getRawOne或者getRawMany方法
    .select('SUM(user.id)', 'sum')
    .getRawOne();
  console.log(result);
  ```

## 五、传递参数

* 1、直接使用

  ```ts
  .where("user.username = :username", { username: "哈哈" })
  ```

* 2、使用`setParameter`填充值

  ```ts
  .where("user.username = :username")
  .setParameter("username", "哈哈")
  ```

* 3、`LIKE`模糊查询

  ```ts
  .where("user.username like :username", {username: `% ${username} %`})
  ```

* 4、`IN`查询

  ```ts
  .where("user.username IN (:...username)", { username: [ "Timber", "Cristal", "Lina" ] })
  ```

## 六、[本章节参考代码](https://github.com/kuangshp/nest-book-code/tree/16.queryBuilder)



----
前面我们使用`synchronize=true`同步实体类到数据表中,在实际开发中你可能会多次更改实体类,但是数据库已经有数据了,你也不可能先删除之前的数据表,然后再来同步,上线后的项目也会把同步功能关闭。以避免生产的数据被删除。解决方式有

* 1、手动写`SQL`语句增加列到数据表中,然后再实体类中添加对应的字段值。对于没`SQL`基础的同学来说可能有点难度
* 2、采用手动迁移的方式,如果你曾经用过`django`的,就知道这功能。直接写好实体类,简单运行几个命令就同步到数据库

## 一、配置命令

> 可能你记住不那么多命令,但是我们`node`项目都可以把命令配置到`package.json`文件

  ```json
  {
    "scripts": {
      "start": "ts-node src/index.ts",
      "generate": "ts-node  ./node_modules/typeorm/cli.js migration:generate -n Test",
      "db": "ts-node ./node_modules/typeorm/cli.js migration:run"
    }
  }
  ```

* 1、我们每次修改实体类的时候先后运行命令

  ```shell
  # 注意顺序执行
  npm run generate
  npm run db
  ```
* 2、[代码参考](https://github.com/kuangshp/nest-book-code/tree/17.migration)
