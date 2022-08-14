---
uuid: 851a1520-120c-11ed-b6ad-296d47d41d06
title: cli
abbrlink: 1224102205
date: 2022-07-24 23:47:13
---

工具安装

1. 安装 docker

   桌面版:  [docker官网](https://www.docker.com/)

2. 安装 bsam cli

   执行 `sudo pip3 install bce-sam-cli` 即可完成安装。

   > 遇到问题： `zsh: command not found: bsam` ; 
   > 原因是之前没有加 sudo

创建函数

1. 初始化项目： `bsam init --runtime nodejs12 --name lee-app`

2. 函数安装依赖：

   1. `cd src` `npm install`
   2. `bsam local install` 

3. 部署

   1. 打包 `bsam package`
   2. 发布 `bsam deploy`
      > 发布之前要去配置 config， 把百度帐号的信息跟本地的cli绑定起来， [创建/获取 AK/SK](https://console.bce.baidu.com/iam/?_=1658676423284#/iam/accesslist)
      > 执行 `bsam config`
      > BCE Access Key ID:
      > BCE Secret Access Key:
      > BCE region (bj, gz, su):

   3. 执行完deploy命令后， 可到页面函数列表里边去看函数是否发布成功， 如果成功了， 就会在函数列表里边有

4. 测试

   1. 本地
      1. 去src下边用 [mocha](https://mochajs.org/) 进行测试， 执行 `npm run test`
   2. [CFC 函数列表](https://console.bce.baidu.com/cfc/?#/cfc/functions)可以进行测试
      点击函数列表后面的测试操作就可以

5. 调用

   1. 添加一个触发器
   2. http的触发器可以直接在浏览器地址栏访问览器地址栏访问