---
title: hello hexo
date: 2019-11-21 18:00:10
tags:
  hexo
categories:
  tools
---



### 在 hexo 使用过程中遇到的一点小坑



本站使用主题使用的是 [next](https://github.com/theme-next/hexo-theme-next)  版本是 7.5.0

到了这个版本，gitalk 是集成在 next 中的， 所以使用起来很方便：

但是还是遇到了坑：

<!-- more -->

### 未找到相关的 Issues 进行评论 请联系 @admin 初始化创建

#### 分析：

​	找不到，肯定是路径的问题，经过自己反复对比api 发现

**我在配置OAuth Apps 的时候，Homepage URL 跟 Authorization callback URL 后面加了一个"/"**

![](/images/hexo/oauthapp.png)

造成后面加了"/"的原因是因为我写 URL 的时候，是直接人浏览器地址栏复制过来的， 默认带"/"



#### 解决办法：

去掉"/",  点击 Update application 按钮， 等个三五分钟， 就可以了