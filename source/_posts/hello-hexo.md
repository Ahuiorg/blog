---
title: hello hexo
tags: hexo
categories: tools
abbrlink: 1310372071
date: 2019-11-21 18:00:10
---

### 记录在使用 hexo 过程中的一些心得

<!-- more -->
### 2020年3月21日

给文章添加目录

修改 `themes/next/_config.yml` 下 `toc.expand_all` 为 `true`

### 2020年2月３０

想要首行缩进的时候，必须用全角空格，半角空格是不能有效缩进的

实现首行缩进也可以用 `&nsbp;`

### 2020年2月29

编写`markdown`文档的时候，如果想给写的内容加样式，其实完全可以直接用`html`的语法写

比如： 

```html
<img src="/images/web/js/proto.jpg" align="center" style="margin: 0 auto;">
```

这样就可以引入一张图片并居中显示图片

### 在 hexo 使用过程中遇到的一点小坑

本站使用主题使用的是 [next](https://github.com/theme-next/hexo-theme-next) 版本是 7.5.0

到了这个版本，gitalk 是集成在 next 中的， 所以使用起来很方便：

但是还是遇到了坑：



### 未找到相关的 Issues 进行评论 请联系 @admin 初始化创建

#### 分析：

​ 找不到，肯定是路径的问题，经过自己反复对比 api 发现

**我在配置 OAuth Apps 的时候，Homepage URL 跟 Authorization callback URL 后面加了一个"/"**

![](/images/hexo/oauthapp.png)

造成后面加了"/"的原因是因为我写 URL 的时候，是直接人浏览器地址栏复制过来的， 默认带"/"

#### 解决办法：

去掉"/", 点击 Update application 按钮， 等个三五分钟， 就可以了
