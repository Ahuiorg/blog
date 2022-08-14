---
uuid: 49c331f9-120c-11ed-aacc-b11deac0aae6
title: 让 hexo URL更加优雅
abbrlink: 3651303969
date: 2020-01-14 21:37:19
tags:
---

大家都知道 hexo url 默认是通过日期加标题确定的，这样的 url 特别难看

<!-- more -->

比如：

```
http://didiorg.com/2020/01/13/%E5%B4%87%E7%A4%BC%E5%A4%AA%E8%88%9E%E6%BB%91%E9%9B%AA/
```

说实话，看不懂，因为把后面的汉字转义了

这个时候 hexo 官方提供了一种解决方案：

```json
# permalink: :year/:month/:day/:title/
# 改成：
permalink: :id.html
```

就有了下面这个：

```
http://didiorg.com/ck5dxcj8p00094dvzd8sirsit.html
```

这样其实也非常不友好

> 第一，url 还是太长了。
> 第二，也是最重要的一点，每“hexo g”一次，“`:id`”生成的 url 都是不一样的，这非常影响 SEO。

#### 只推荐一种方案，就是安装 hexo-abbrlink 插件

```
npm hexo-abbrlink --save
```

再修改一下配置文件

```json
permalink: :abbrlink.html # 生成唯一链接
abbrlink:
  alg: crc32 # 算法：crc16(default) and crc32
  rep: dec # 进制：dec(default) and hex
```

这个时候的 url 就很好看了

比如：

```
http://didiorg.com/10086.html
```

> 该插件的原理是在文章中插入一个“`abbrlink`”参数，参数值是使用算法生成的 id，根据该“`abbrlink`”参数的值生成文章的固定 url，从而实现文章 url 固定。

看下面的文章，

```
---
title: 2020健身记录
tags: 健身
categories: fit
top: true
abbrlink: 10086
date: 2020-01-09 09:58:39
---
```

这个插件确实会生成一个唯一的 abbrlink， 这样挺好， 但是，我觉得更好的是， 这个 abbrlink 后面的值是可以自己修改的， 你可以改成数据， 改成字母，都可以， 这样就极大的方便我们管理我们的 url 了，比如上边这个链接，我就把原来生成的 id 改成了 10086， 很友好。

再看下面一个：

```
---
title: 崇礼太舞滑雪
tags: ski
abbrlink: thaiwooski
date: 2020-01-13 09:54:15
---
```

url 为：

```
http://didiorg.com/thaiwooski.html
```
