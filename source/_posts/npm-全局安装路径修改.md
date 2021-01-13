---
title: npm 全局安装路径修改
date: 2021-01-13 17:34:56
tags: node
category: 前端
cover: '/img/desk.png'
---
# NPM修改默认全局安装路径
> 有段时间没用npm了，昨天打算用vue-cli来新建个项目，yarn global add @vue/cli 之后，粗大事了。vue命令不可用，显示未安装。各种折腾，后来发现问题所在，今天写出来，一方面提醒自己，一方面利于他人。
> 首先，默认的全局安装路径，我忘了，哈哈，因为很久之前自己小手一抖，改过了，即使重装nodejs，都不能复原到默认路径（这一点好蛋疼）
> 其次，因为我重新安装了nodejs，所以在我用yarn安装后，无法安装到npm指定的地方，虽然yarn config get prefix得到的路径和npm config get prefix是一样的，但是安装的时候还有安装到了nodejs/bin文件下，在nodejs/node_modules下并没有
> 最后，说一下完整的设置方法：(我的nodejs安装路径为D:\nodejs)
----
- 在D:\nodejs文件中新建文件夹node_cache，而后打开cmd，npm config set cache "D:\nodejs\node_cache"
- 继续在cmd中运行 npm config set prefix "D:\nodejs"，这里面本来就有node_modules文件夹了，全局安装时，会自动找这个文件夹，所以不用我们手动新建文件夹
- 修改D:\nodejs\node_modules\npm\npmrc 文件为 prefix=D:\nodejs。这个时候，全局安装后，使用时，才能真正的从我们设置的地方找，也就不会出现安装完，使用不了的情况了