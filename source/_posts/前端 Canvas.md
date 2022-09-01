---
title: Canvas 不完全讲解
abbrlink: 4000f72d
date: 2022-08-30 17:42:18
tags: Canvas
---

# Canvas 概述

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ac59876df8024afb8a679fabf7977ef7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

从翻译其实就能很好的了解到 canvas 是用来干什么的。画布！很好理解就是用来画画的。那具体怎么“画”咱们就得看一下具体的 API 文档了。下面是对 canvas 的一些概述：

- **canvas** 是一个可以使用脚本(通常为JavaScript)来绘制图形的 HTML 元素.
- **Canvas API** 提供了一个通过JavaScript 和 HTML的Canvas元素来绘制图形的方式。它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面。
- **Canvas API** 主要聚焦于2D图形。而同样使用Canvas元素的 WebGL API 则用于绘制硬件加速的2D和3D图形。

# Canvas使用

Canvas 最早是由 Apple 引入 WebKit，用于Mac OS X 的 Dashboard，随后被各个浏览器实现。如今除一些过时的浏览器不支持Canvas元素外，所有的新版本主流浏览器都支持它。

Canvas元素的学习需要具备一些基本的HTML和JavaScript知识。

## 基本用法

下面我们来简单创建一个例子，看看canvas究竟如何使用。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas 基本使用</title>
</head>
<body>
  <canvas width="200" height="200">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
</body>
</html>
复制代码
```

上面代码就是一个基本的使用Canvas标签的例子。可以看到我们为它设置了宽和高，还在 Canvas标签内部给出一个提示文案。在这里需要说明一下：

- Canvas标签的默认大小为：300 x 150 (像素)，而这里咱们设置为了：200 x 200（像素）。
- Canvas标签中的文字是在不支持Canvas标签的浏览器中使用的，因为支持Canvas标签的浏览器会忽略容器中包含的内容正常渲染Canvas标签，而不支持Canvas标签的浏览器则相反，浏览器会忽略容器而显示其中的内容。

可以看一下上面代码在浏览器上的展示样式：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8338aa0579540079d7492a31c6f9c6f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

## 渲染上下文

Canvas标签起初只是创造了一个固定大小的画布，它公开了一个或多个渲染上下文，而我们想对它进行绘制就需要找到渲染上下文。

Canvas标签提供了一个方法叫：**getContext()** ，通过它我们可以获得渲染上下文和绘画功能。简单写个例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas 基本使用</title>
</head>
<body>
  <canvas id="canvas" width="200" height="200">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
    }
  </script>
</body>
</html>
复制代码
```

这里需要注意一点，getContext方法是有一个接收参数，它是绘图上下文的类型，可能的参数有：

- 2d：建立一个二维渲染上下文。这种情况可以用 CanvasRenderingContext2D()来替换getContext('2d')。
- webgl（或 experimental-webgl）： 创建一个 WebGLRenderingContext 三维渲染上下文对象。只在实现WebGL 版本1(OpenGL ES 2.0)的浏览器上可用。
- webgl2（或 experimental-webgl2）：创建一个 WebGL2RenderingContext 三维渲染上下文对象。只在实现 WebGL 版本2 (OpenGL ES 3.0)的浏览器上可用。
- bitmaprenderer：创建一个只提供将canvas内容替换为指定ImageBitmap功能的ImageBitmapRenderingContext。

## 绘制形状

在我们获得绘制上下文以后，就可以根据绘制上下文开始绘制一些基本的形状，比如：直线、三角形、矩形、圆弧和圆。接下来咱们具体实现一下。

### 直线

绘制直线咱们需要了解三个函数：

#### moveTo(x, y)

设置初始位置，参数为初始位置x和y的坐标点

#### lineTo(x, y)

绘制一条从初始位置到指定位置的直线，参数为指定位置x和y的坐标点

#### stroke()

通过线条来绘制图形轮廓

接下里咱们应用上面的三个函数来试着绘制一条直线，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制直线</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一条从起点（x: 50, y:50）到 另一个点（x: 200, y:200）的直线
      ctx.moveTo(50, 50);
      ctx.lineTo(200, 200);
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

为了展示的效果好一点，这里我调整了一下画布的大小：500 x 500，还给画布添加了一个阴影和圆角。得到的直线如图：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5b87e730f6e14652b1b39d5243087015~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 三角形

知道了如何绘制一条直线，那么绘制三角形也就不难了，咱们只需要画三条直线拼在一起就是一个三角形了，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制三角形</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一个三角形
      ctx.moveTo(50, 50);
      ctx.lineTo(200, 200);
      ctx.lineTo(200, 50);
      ctx.lineTo(50, 50);
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

具体效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cd1e5ccce89a45b1ad42014996d1a4fb~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 矩形

知道了三角形的绘制，那么矩形的绘制是不是也用直线来拼凑呢？答案是否定的，矩形虽然可以用四条直线来拼凑成，但那样太复杂了，Canvas API 给提供了三种绘制矩形的方法：

- strokeRect(x, y, width, height) 绘制一个矩形的边框
- fillRect(x, y, width, height) 绘制一个填充的矩形
- clearRect(x, y, width, height) 清除指定矩形区域，让清除部分完全透明。

下面我们依次看一下他们有什么异同。

#### strokeRect

strokeRect(x, y, width, height) 是用来绘制一个矩形的边框，x和y 是矩形的起点坐标，width和height 是矩形的宽高。举个例子，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制矩形</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一个矩形边框
      ctx.strokeRect(50, 50, 200, 100);
    }
  </script>
</body>
</html>
复制代码
```

如下图，strokeRect方法绘制的就是一个矩形框：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/991a1b38361c45c198bf4c4c6c032a6b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### fillRect

fillRect(x, y, width, height) 绘制一个填充的矩形，x和y 是矩形的起点坐标，width和height 是矩形的宽高。举个例子，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制矩形</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一个填充矩形
      ctx.fillRect(100, 100, 200, 100);
    }
  </script>
</body>
</html>
复制代码
```

如下图，fillRect方法实现的是填充了一个矩形：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a696429cf6a642f889fb1e34210b7716~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### clearRect

clearRect(x, y, width, height) 清除指定矩形区域，让清除部分完全透明，x和y 是矩形的起点坐标，width和height 是矩形的宽高。这里需要结合结合另外两种画法来对比一下，才能看出具体的效果，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制矩形</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一个填充矩形
      ctx.fillRect(100, 100, 200, 100);
      ctx.fillRect(50, 50, 200, 100);
      ctx.clearRect(75, 75, 100, 70);
    }
  </script>
</body>
</html>
复制代码
```

如下图，中间白色的矩形就是被指定清除的区域：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/41a18bd6d9e44d5fa0b7d79363ab6341~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 圆弧和圆

绘制圆弧或者圆，使用的方法是：arc(x, y, radius, startAngle, endAngle, anticlockwise)。x和Y为圆心的坐标，radius为半径，startAngle为圆弧或圆的开始位置，endAngle为圆弧或圆的结束位置，anticlockwise是绘制的方向（不写默认为false，从顺时针方向）。

下面画一个半圆弧看看效果，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制圆弧</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一段圆弧
      ctx.arc(60, 60, 50, 0, Math.PI, false);
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

效果如下图：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb8defb06b464d58add071692fe3735f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

这里需要注意的是：在画弧的时候，arc()函数中角的单位是弧度而不是角度。角度换算为弧度的表达式为：弧度=(Math.PI/180)*角度。

所以想要画一个圆的弧度就是：Math.PI*2，咱们继续画一个圆弧看一下，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制圆弧</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一段圆弧
      ctx.arc(60, 60, 50, 0, Math.PI, false);
      ctx.stroke();
      // 绘制一个圆弧
      ctx.arc(200, 60, 50, 0, Math.PI*2, false);
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

但效果似乎不想我们想象的一样，如下图：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/569a2911256b409494497788f4d36c03~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

如上图所示，先画的半圆弧和后画的圆弧被连在了一起，其实这是因为在咱们每次新建路径的时候都需要开启和闭合路径，这样不同路径之间才不会相互干扰。下面咱们就来介绍一下如何开启和闭合路径。

#### beginPath

新建一条路径，生成之后，图形绘制命令被指向到路径上。

#### closePath

闭合路径之后图形绘制命令又重新指向到上下文中。 具体怎么使用这两个函数呢？下面咱们介绍一下，直接上代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制圆弧</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一段圆弧
      ctx.beginPath() // 开启路径
      ctx.arc(60, 60, 50, 0, Math.PI, false);
      ctx.stroke();
      ctx.closePath() // 闭合路径
      // 绘制一个圆弧
      ctx.beginPath() // 开启路径
      ctx.arc(200, 60, 50, 0, Math.PI*2, false);
      ctx.stroke();
      ctx.closePath() // 闭合路径
    }
  </script>
</body>
</html>
复制代码
```

如上代码，咱们为每一条路径都设置了开启和闭合。那么看一下效果如何：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/046de181d48d43af9d49b1013f2a5ddc~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

这里有一点需要说明一下，其实在咱们开启和关闭路径的时候，关闭路径其实并不是必须的，对于新路径其实每次都开启新路径就ok。

以上其实都是通过stroke方法来做描边，那么如果想填充有没有对应的方法呢？

#### fill

stroke方法是通过线条来绘制图形轮廓，而fill方法则是通过填充路径的内容区域生成实心的图形。

具体如何使用举个例子看一下。代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 填充</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一段圆弧
      ctx.beginPath() // 开启路径
      ctx.arc(60, 60, 50, 0, Math.PI, false);
      ctx.stroke();
      // 绘制一个圆弧
      ctx.beginPath() // 开启路径
      ctx.arc(200, 60, 50, 0, Math.PI*2, false);
      ctx.stroke();
      // 填充一个四分之一圆弧
      ctx.beginPath() // 开启路径
      ctx.arc(60, 200, 50, 0, Math.PI/2, false);
      ctx.fill();
      // 填充一个半圆弧
      ctx.beginPath() // 开启路径
      ctx.arc(200, 200, 50, 0, Math.PI, false);
      ctx.fill();
      // 填充一个圆弧
      ctx.beginPath() // 开启路径
      ctx.arc(350, 200, 50, 0, Math.PI*2, false);
      ctx.fill();
    }
  </script>
</body>
</html>
复制代码
```

效果如下图：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/67227bfbf96d4399928a393ed37a4526~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 椭圆

添加椭圆路径。

语法：ellipse(x, y, radiusX, radiusY, rotation, startAngle, endAngle, anticlockwise)

- x、y：椭圆的圆心位置
- radiusX、radiusY：x轴和y轴的半径
- rotation：椭圆的旋转角度，以弧度表示
- startAngle：开始绘制点
- endAngle：结束绘制点
- anticlockwise：绘制的方向（默认顺时针），可选参数。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 裁剪</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.beginPath();
      ctx.ellipse(100, 150, 50, 100, 0, 0, 2 * Math.PI);
      ctx.stroke();
      ctx.beginPath();
      ctx.ellipse(400, 150, 50, 100, 0, 0, 2 * Math.PI);
      ctx.stroke();
      ctx.beginPath();
      ctx.ellipse(250, 350, 50, 100, Math.PI/2, 0, 2 * Math.PI); // 旋转90°
      ctx.fill();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c8f129671f5e4941b9189fa2c6cde821~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 贝塞尔曲线

贝塞尔曲线一般用来绘制复杂有规律的图形，在Canvas中也是一个十分有用的路径类型。

#### 二次贝塞尔曲线

语法：quadraticCurveTo(cp1x, cp1y, x, y)，其中cp1x和cp1y为一个控制点，x和y为结束点。

举个例子，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制二次贝塞尔曲线</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一段二次贝塞尔曲线
      ctx.moveTo(50, 50);
      ctx.quadraticCurveTo(200, 200, 350, 50);
      // 绘制
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

得到的效果图如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/683b564d336c4cb4a4fc6e59bf0df4be~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

如上图，一段二次贝塞尔曲线是通过一个起点、终点和结束点来控制的。下面通过控制点的变化来看一下二次贝塞尔曲线的变化。

把控制点往左移100像素点，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制二次贝塞尔曲线</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一段二次贝塞尔曲线
      ctx.beginPath() // 开启路径
      ctx.moveTo(50, 50);
      ctx.quadraticCurveTo(200, 200, 350, 50);
      // 绘制
      ctx.stroke();
      // 绘制一段二次贝塞尔曲线
      ctx.beginPath() // 开启路径
      ctx.moveTo(50, 250);
      ctx.quadraticCurveTo(100, 400, 350, 250);
      // 绘制
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a718a84ca7684686b74c25edd71164d0~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

这个其实可以借助一个网页版的[二次贝塞尔曲线调试工具](https://link.juejin.cn/?target=http%3A%2F%2Fblogs.sitepointstatic.com%2Fexamples%2Ftech%2Fcanvas-curves%2Fquadratic-curve.html)来看一下效果

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9b256d08542549acbfd8556267abe09c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### 三次贝塞尔曲线

和二次贝塞尔曲线不同的是三次贝塞尔曲线有两个控制点。

语法：ctx.bezierCurveTo(cp1x,cp1y, cp2x,cp2y, x, y)，其中cp1x和cp1y为一个控制点，cp2x和cp2y为第二个控制点，x和y为结束点。

举个例子，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制三次贝塞尔曲线</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一段三次贝塞尔曲线
      ctx.beginPath() // 开启路径
      ctx.moveTo(50, 200);
      ctx.bezierCurveTo(150, 50, 250, 350, 350, 200);
      // 绘制
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e2e4cd7dd54456e90dc278616e2d708~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

这里也可以借助一个网页版的[三次贝塞尔曲线调试工具](https://link.juejin.cn/?target=http%3A%2F%2Fblogs.sitepointstatic.com%2Fexamples%2Ftech%2Fcanvas-curves%2Fbezier-curve.html)来看一下效果：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e259e475fdaf414ebb5f270aab09efa8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

## 绘制样式

在上面的图形绘制中都只是默认的样式。接下来说一下具体有哪些绘制样式。

### 线条的样式

线条的样式可以通过下面一系列属性来设置。

#### lineWidth

lineWidth 设置当前绘线的粗细。属性值必须为正数。默认值是 1.0。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制样式</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="300" height="300">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一条宽度为10的直线
      ctx.beginPath()
      ctx.lineWidth = 10;
      ctx.moveTo(50, 20);
      ctx.lineTo(250, 20);
      ctx.stroke();
      ctx.closePath();
      // 绘制一条宽度为20的直线
      ctx.beginPath()
      ctx.lineWidth = 20;
      ctx.moveTo(50, 50);
      ctx.lineTo(250, 50);
      ctx.stroke();
      ctx.closePath();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8071fe30a17947a8a4c33af44b26bcec~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### lineCap

lineCap 设置线段端点显示的样子。可选值为：butt，round 和 square。默认是 butt。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制样式</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="300" height="300">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // lineCap 值为 butt
      ctx.beginPath()
      ctx.lineWidth = 10;
      ctx.lineCap='butt'
      ctx.moveTo(50, 20);
      ctx.lineTo(250, 20);
      ctx.stroke();
      ctx.closePath();
      // lineCap 值为 round
      ctx.beginPath()
      ctx.lineWidth = 10;
      ctx.lineCap='round'
      ctx.moveTo(50, 50);
      ctx.lineTo(250, 50);
      ctx.stroke();
      ctx.closePath();
      // lineCap 值为 square
      ctx.beginPath()
      ctx.lineWidth = 10;
      ctx.lineCap='square'
      ctx.moveTo(50, 80);
      ctx.lineTo(250, 80);
      ctx.stroke();
      ctx.closePath();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5030925e1429474080b32760128fae2c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### lineJoin

lineJoin 该属性可以设置两线段连接处所显示的样子。可选值为：round, bevel 和 miter。默认是 miter。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制样式</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="300" height="300">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // lineJoin 值为 miter
      ctx.beginPath()
      ctx.lineWidth = 10;
      ctx.lineJoin='miter'
      ctx.moveTo(50, 20);
      ctx.lineTo(100, 60);
      ctx.lineTo(150, 20);
      ctx.lineTo(200, 60);
      ctx.lineTo(250, 20);
      ctx.stroke();
      ctx.closePath();
      // lineJoin 值为 round
      ctx.beginPath()
      ctx.lineWidth = 10;
      ctx.lineJoin='round'
      ctx.moveTo(50, 100);
      ctx.lineTo(100, 140);
      ctx.lineTo(150, 100);
      ctx.lineTo(200, 140);
      ctx.lineTo(250, 100);
      ctx.stroke();
      ctx.closePath();
      // lineJoin 值为 bevel
      ctx.beginPath()
      ctx.lineWidth = 10;
      ctx.lineJoin='bevel'
      ctx.moveTo(50, 180);
      ctx.lineTo(100, 220);
      ctx.lineTo(150, 180);
      ctx.lineTo(200, 220);
      ctx.lineTo(250, 180);
      ctx.stroke();
      ctx.closePath();
    }
  </script>
</body>
</html>
复制代码
```

效果为：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/75501ff07c904eff8183be292e51a17e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### miterLimit

miterLimit 限制当两条线相交时交接处最大长度；所谓交接处长度（斜接长度）是指线条交接处内角顶点到外角顶点的长度。

线段之间夹角比较大时，交点不会太远，但随着夹角变小，交点距离会呈指数级增大。

如果交点距离大于miterLimit值，连接效果会变成了 lineJoin = bevel 的效果。

举个例子看一下

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制样式</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="300" height="300">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // miterLimit为定值，角度越大
      ctx.beginPath()
      ctx.lineWidth = 5;
      ctx.lineJoin='miter'
      ctx.miterLimit = 10
      ctx.moveTo(0, 100);
      for (i = 0; i < 30 ; i++) {
        var dy = i % 2 == 0 ? 200 : 100;
        ctx.lineTo(Math.pow(i, 1.5) * 2, dy);
      }
      ctx.stroke();
      ctx.closePath();
    }
  </script>
</body>
</html>
复制代码
```

效果为：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fb83a5c3fbeb46b19c855519b8998ed2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### setLineDash/getLineDash

setLineDash 可以设置当前虚线样式。

getLineDash 则是返回当前虚线设置的样式，长度为非负偶数的数组。

举个例子看一下

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制虚线</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一条虚线
      ctx.setLineDash([5, 10, 20]);
      console.log(ctx.getLineDash()); // [5, 10, 20, 5, 10, 20]
      ctx.beginPath();
      ctx.moveTo(0,100);
      ctx.lineTo(400, 100);
      ctx.stroke();
      // 再绘制一条虚线
      ctx.setLineDash([5, 10, 20, 40]);
      console.log(ctx.getLineDash()); // [5, 10, 20, 40]
      ctx.beginPath();
      ctx.moveTo(0,200);
      ctx.lineTo(400, 200);
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

先看效果再讲解，效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/24e65e0703c847c1a56fa41cc3026329~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

首先这里画两条虚线是想对比一下传参为奇数数组和偶数数组的区别，在我们设置虚线的时候，如果传参为奇数，例如：ctx.setLineDash([5, 10, 20])，那么 setLineDash 会复制一份数组补全为偶数，相当于我们设置的是：ctx.setLineDash([5, 10, 20, 5, 10, 20])。所以这也就是为什么上图中我们设置的是 [5, 10, 20]，结果打印出来是 [5, 10, 20, 5, 10, 20]

#### lineDashOffset

lineDashOffset 设置虚线样式的起始偏移量。

这里咱们再画第三条虚线来对比一下

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制虚线</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一段圆弧
      ctx.setLineDash([5, 10, 20]);
      console.log(ctx.getLineDash()); // [5, 10, 20, 5, 10, 20]
      ctx.beginPath();
      ctx.moveTo(0,100);
      ctx.lineTo(400, 100);
      ctx.stroke();
      ctx.setLineDash([5, 10, 20, 40]);
      console.log(ctx.getLineDash()); // [5, 10, 20, 40]
      ctx.beginPath();
      ctx.moveTo(0,200);
      ctx.lineTo(400, 200);
      ctx.stroke();
      ctx.setLineDash([5, 10, 20, 40]);
      ctx.lineDashOffset = 3;
      ctx.beginPath();
      ctx.moveTo(0,300);
      ctx.lineTo(400, 300);
      ctx.stroke();
    }
  </script>
</body>
</html>
复制代码
```

效果为：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5fbdfa2b8bfc4a74ae7847fa9ed4e5d1~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

这里可以明显看出虚线的总长度没有变化，只是起始点向左位移了3像素。

### 透明度

除了绘制实色的图形，还可以绘制有透明度的图形。通过设置 globalAlpha 属性或者使用有透明度的样式作为轮廓或填充都可以实现

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 设置透明度</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 绘制一个矩形
      ctx.beginPath();
      // 指定透明度的填充样式
      ctx.fillStyle = "rgba(0, 255, 0, 0.2)";
      ctx.fillRect(10,10,300,100);
      // 绘制一个矩形边框
      ctx.beginPath();
      // 指定透明度的描边样式
      ctx.strokeStyle = "rgba(255, 0, 0, 0.7)";
      ctx.strokeRect(10, 90, 100, 300);
      // 绘制一个圆
      ctx.beginPath()
      ctx.fillStyle = "rgba(255, 255, 0, 1)";
      // 设置透明度值
      ctx.globalAlpha = 0.5;
      ctx.arc(200, 200, 100, 0, Math.PI*2, true);
      ctx.fill();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/df94b316ad7042cd8e487b33838d14a4~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 渐变

渐变分为两种，分别是线性渐变和径向渐变，在绘图中我们可以用线性或者径向的渐变来填充或描边。

#### 线性渐变

语法： createLinearGradient(x1, y1, x2, y2)，参数分别为 起点的坐标和终点的坐标。

在渐变的设置中还需要一个方法来添加渐变的颜色，语法为：gradient.addColorStop(offset, color)，其中color就是颜色，offset 则是颜色的偏移值，只为0到1之间的数。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 渐变</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 创建渐变
      var gradient1 = ctx.createLinearGradient(10, 10, 400, 10);
      gradient1.addColorStop(0, "#00ff00");
      gradient1.addColorStop(1, "#ff0000");
      var gradient2 = ctx.createLinearGradient(10, 10, 400, 10);
      // 从0.5的位置才开始渐变
      gradient2.addColorStop(0.5, "#00ff00");
      gradient2.addColorStop(1, "#ff0000");
      ctx.beginPath()
      ctx.fillStyle = gradient1;
      ctx.fillRect(10, 10, 400, 100);
      ctx.beginPath();
      ctx.fillStyle = gradient2;
      ctx.fillRect(10, 150, 400, 100);
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ef384c3981a4f28aa1ee4ad203d2d25~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### 径向渐变

语法：ctx.createRadialGradient(x0, y0, r0, x1, y1, r1)，参数分别为开始圆的坐标和半径以及结束圆的坐标和半径。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 渐变</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 创建渐变
      // 结束坐标为点
      var gradient1 = ctx.createRadialGradient(100, 100, 100, 100, 100, 0);
      gradient1.addColorStop(0, "#ff770f");
      gradient1.addColorStop(1, "#ffffff");
      // 结束坐标为半径30的圆
      var gradient2 = ctx.createRadialGradient(320, 100, 100, 320, 100, 30); 
      gradient2.addColorStop(0, "#ff770f");
      gradient2.addColorStop(1, "#ffffff");
      // 从0.5的位置才开始渲染
      var gradient3 = ctx.createRadialGradient(100, 320, 100, 100, 320, 0); 
      gradient3.addColorStop(0.5, "#ff770f"); 
      gradient3.addColorStop(1, "#ffffff");
      // 开始坐标和结束坐标不一样
      var gradient4 = ctx.createRadialGradient(320, 320, 100, 250, 250, 0);
      gradient4.addColorStop(0, "#ff770f");
      gradient4.addColorStop(1, "#ffffff");
      ctx.beginPath();
      ctx.fillStyle = gradient1;
      ctx.fillRect(10, 10, 200, 200);
      ctx.beginPath();
      ctx.fillStyle = gradient2;
      ctx.fillRect(220, 10, 200, 200);
      ctx.beginPath();
      ctx.fillStyle = gradient3;
      ctx.fillRect(10, 220, 200, 200);
      ctx.beginPath();
      ctx.fillStyle = gradient4;
      ctx.fillRect(220, 220, 200, 200);
    }

  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c816845ee613437990d6a5d635a25a4e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 图案样式

Canvas中想绘制图案效果，需要用 createPattern 方法来实现。

语法：createPattern(image, type)，参数分别为：Image 参数可以是一个 Image 对象，也可以是一个 canvas 对象，Type 为图案绘制的类型，可用的类型分别有：repeat，repeat-x，repeat-y 和 no-repeat。

首先先看一下如何应用 Image 对象来绘制图案。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制图案</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 创建一个 image对象
      var img = new Image();
      img.src = "./image.png";
      img.onload = function() {
        // 图片加载完以后
        // 创建图案
        var ptrn = ctx.createPattern(img, 'no-repeat');
        ctx.fillStyle = ptrn;
        ctx.fillRect(0, 0, 500, 500);
      }
    }
  </script>
</body>
</html>
复制代码
```

上面是一个用image对象绘制的例子，效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/df578b376ccd485da936c244b0f002a2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

从上面的代码我们可以看出，本来我们想填充的是一个500*500的长方形，但是因为咱们绘制的类型设置为不平铺（no-repeat）所以看到的效果不能让我们满意，那么咱们分别看看这四个类型分别是什么效果。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制图案</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 创建一个 image对象
      var img = new Image();
      img.src = "./image.png";
      img.onload = function() {
        // 图片加载完以后
        // 创建图案
        var ptrn = ctx.createPattern(img, 'repeat');
        ctx.fillStyle = ptrn;
        ctx.fillRect(0, 0, 500, 500);
      }
    }
  </script>
</body>
</html>
复制代码
```

设置为平铺（repeat），效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1cb18941591d4d809c58f462e8b8aeb0~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)这其实才是我们想要的效果，那么咱们再看看沿X轴平铺（repeat-x）和沿Y轴平铺（repeat-y）

效果分别是：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6a2afe857fb74b77b1f3f6fd740f6e1b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28bea7b8fae04ca7a1cafd1198d995b7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

最后看一下如何应用 canvas 对象来绘制图案。 举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制图案</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
      margin-right: 50px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="200" height="200">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <canvas id="canvas2" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    var canvas2 = document.getElementById('canvas2');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext && canvas2.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var ctx2 = canvas2.getContext('2d');
      // 创建一个 canvas对象
      var img = new Image();
      img.src = "./image.png";
      img.onload = function() {
        // 图片加载完以后
        // 创建图案
        var ptrn = ctx.createPattern(img, 'repeat');
        ctx.fillStyle = ptrn;
        ctx.fillRect(0, 0, 200, 200);
        // 用canvas来绘制canvas2
        var ptrn2 = ctx2.createPattern(canvas, 'repeat');
        ctx2.fillStyle = ptrn2;
        ctx2.fillRect(0, 0, 500, 500);
      }
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa38ee8c03f34b6088a7b72e57dfaf9b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

上面的例子可以看出，canvas2是用canvas1来绘制图案的

## 绘制文本

canvas 中依旧提供了两种方法来渲染文本，一种是描边一种是填充。

### strokeText（描边）

语法：ctx.strokeText(text, x, y, maxWidth)参数分别为：

- text：绘制的文案
- x、y：文本的起始位置
- maxWidth：可选参数，最大宽度。需要注意的是当文案大于最大宽度时不是裁剪或者换行，而是缩小字体。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制文本</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.font = "50px serif"; // 设置文案大小和字体
      ctx.strokeText("Canvas 详解", 50, 50);
    }
  </script>
</body>
</html>
复制代码
```

看一下效果：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2cb4a32ebfb84ea583ad45b5211a287c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### fillText（填充）

语法：ctx.fillText(text, x, y, maxWidth)参数分别为：

- text：绘制的文案
- x、y：文本的起始位置
- maxWidth：可选参数，最大宽度。需要注意的是当文案大于最大宽度时不是裁剪或者换行，而是缩小字体。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制文本</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.font = "50px serif"; // 设置文案大小和字体
      ctx.fillText("Canvas 详解", 50, 50);
    }
  </script>
</body>
</html>
复制代码
```

### 文本样式

文本也是可以添加样式的，下面看一下可以设置那些样式

#### font

用于绘制文本的样式。默认的字体是 10px sans-serif。

#### textAlign

文本对齐的方式。可选值为：left、right、center、start和end。默认值是 start。

#### direction

文本的方向。可选值为：ltr（文本方向从左向右）、rtl（文本方向从右向左）、inherit（根据情况继承 Canvas元素或者 Document 。）。默认值是 inherit。

需要注意的是 direction 属性会对 textAlign 属性产生影响。如果 direction 属性设置为 ltr，则textAlign属性的 left 和 start 的效果相同，right 和 end 的效果相同，如果 direction 属性设置为 rtl，则 textAlign属性的 left 和 end 的效果相同，right 和 start 的效果相同。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制图案</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
      margin-right: 50px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="700">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.font = "30px serif"; // 设置文案大小和字体
      ctx.direction = "ltr"; // 文本方向从左向右
      ctx.textAlign = "left"; // 左对齐
      ctx.strokeText("Hi Canvas !", 150, 100);
      ctx.direction = "ltr"; // 文本方向从左向右
      ctx.textAlign = "center"; // 右对齐
      ctx.strokeText("Hi Canvas !", 150, 200);
      ctx.direction = "ltr"; // 文本方向从左向右
      ctx.textAlign = "right"; // 右对齐
      ctx.strokeText("Hi Canvas !", 150, 300);
      ctx.direction = "rtl"; // 文本方向从左向右
      ctx.textAlign = "left"; // 左对齐
      ctx.strokeText("Hi Canvas !", 150, 400);
      ctx.direction = "rtl"; // 文本方向从左向右
      ctx.textAlign = "center"; // 右对齐
      ctx.strokeText("Hi Canvas !", 150, 500);
      ctx.direction = "rtl"; // 文本方向从左向右
      ctx.textAlign = "right"; // 右对齐
      ctx.strokeText("Hi Canvas !", 150, 600);
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f9dc07f52bc44c8d8b9bae8ee8d653f9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### textBaseline

基线对齐选项，决定文字垂直方向的对齐方式。可选值为：top、hanging、middle、alphabetic、ideographic和bottom。默认值是 alphabetic。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制图案</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
      margin-right: 50px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.font = "25px serif"; // 设置文案大小和字体
      ctx.strokeStyle = 'red';
      const baselines = ['top', 'hanging', 'middle', 'alphabetic', 'ideographic', 'bottom'];
      baselines.forEach(function (baseline, index) {
        ctx.textBaseline = baseline;
        let y = 60 + index * 60;
        ctx.beginPath();
        ctx.moveTo(10, y + 0.5);
        ctx.lineTo(500, y + 0.5);
        ctx.stroke();
        ctx.fillText('Hi Canvas, Welcome to my world! (' + baseline + ')', 10, y);
      });
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d67a548b519b43b29d3ab6318172d0df~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

#### measureText

测量文本，返回一个 TextMetrics对象。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制图案</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
      margin-right: 50px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.font = "30px serif"; // 设置文案大小和字体
      ctx.beginPath();
      ctx.strokeText("Hi Canvas !", 150, 100);
      var text = ctx.measureText("Hi Canvas !");
      console.log("🚀 ~ 文案宽度：", text.width)
      ctx.beginPath();
      // 设置了文案最大宽度
      ctx.strokeText("Hi Canvas !", 150, 200, 100);
      var text1 = ctx.measureText("Hi Canvas !");
      console.log("🚀 ~ 文案宽度：", text1.width)
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/55d40c4f309c41c8a0d24a6518ca0a93~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

如上面的效果可以看出，返回的 TextMetrics对象不受最大宽度等外界因素所影响。

那么TextMetrics对象具体有哪些属性？打印看一下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3691a3cb86bd4aed8c854210aae70756~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

属性解析：

- TextMetrics.width：基于当前上下文字体，计算内联字符串的宽度。
- TextMetrics.actualBoundingBoxLeft：从 textAlign 属性确定的对齐点到文本矩形边界左侧的距离，使用 CSS 像素计算；正值表示文本矩形边界左侧在该对齐点的左侧。
- TextMetrics.actualBoundingBoxRight：从 textAlign 属性确定的对齐点到文本矩形边界右侧的距离。
- TextMetrics.fontBoundingBoxAscent：从 textBaseline 属性标明的水平线到渲染文本的所有字体的矩形最高边界顶部的距离。
- TextMetrics.fontBoundingBoxDescent：从 textBaseline 属性标明的水平线到渲染文本的所有字体的矩形边界最底部的距离。
- TextMetrics.actualBoundingBoxAscent：从 textBaseline 属性标明的水平线到渲染文本的矩形边界顶部的距离。
- TextMetrics.actualBoundingBoxDescent：从 textBaseline 属性标明的水平线到渲染文本的矩形边界底部的距离。
- TextMetrics.emHeightAscent：从 textBaseline 属性标明的水平线到线框中 em 方块顶部的距离。
- TextMetrics.emHeightDescent：从 textBaseline 属性标明的水平线到线框中 em 方块底部的距离。
- TextMetrics.hangingBaseline：从 textBaseline 属性标明的水平线到线框的 hanging 基线的距离。
- TextMetrics.alphabeticBaseline：从 textBaseline 属性标明的水平线到线框的 alphabetic 基线的距离。
- TextMetrics.ideographicBaseline：从 textBaseline 属性标明的水平线到线框的 ideographic 基线的距离。

PS：以上所有属性都是使用 CSS 像素计算的，并且都是只读。

### 阴影

#### shadowOffsetX、shadowOffsetY

shadowOffsetX 和 shadowOffsetY 用来设定阴影在 X 和 Y 轴的延伸距离，它们是不受变换矩阵所影响的。负值表示阴影会往上或左延伸，正值则表示会往下或右延伸，它们默认都为 0。

#### shadowBlur

shadowBlur 用于设定阴影的模糊程度，其数值并不跟像素数量挂钩，也不受变换矩阵的影响，默认为 0。

#### shadowColor

shadowColor 是标准的 CSS 颜色值，用于设定阴影颜色效果，默认是全透明的黑色。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 阴影</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.font = "50px serif"; // 设置文案大小和字体
      ctx.shadowColor = "#cccccc"; //  设置阴影颜色
      ctx.fillStyle = "#ee7934"; //  设置填充颜色
      ctx.shadowOffsetX = 10; // X轴上的阴影
      ctx.shadowOffsetY = 10; // Y轴上的阴影
      ctx.shadowBlur = 5; // 阴影的模糊程度
      ctx.fillText("Hi Canvas !", 100, 50);
      ctx.fillRect(100, 100, 200, 100);
      ctx.shadowOffsetX = -10;
      ctx.shadowOffsetY = -10;
      ctx.shadowBlur = 5;
      ctx.fillText("Hi Canvas !", 100, 300);
      ctx.fillRect(100, 350, 200, 100);
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a8d69341f5764802b3e5480138db0461~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

## 绘制图片

绘制图片和上面的图案样式绘制基本大同小异，不同的是所用方式不一样，绘制图片是使用 drawImage 方法将它渲染到 canvas 里。

### drawImage

这里咱们主要要说的就是drawImage方法的使用，他的用法有三种，是根据不同的传参实现不同的功能。先看看都有哪些参数：

- image：绘制到上下文的元素。
- sx、sy：裁剪框的左上角X轴坐标和Y轴坐标。
- sWidth、sHeight：裁剪框的宽度和高度。
- dx、dy：绘制到上下文的元素，在上下文中左上角的X轴坐标和Y轴坐标。
- dWidth、dHeight：绘制到上下文的元素，在上下文中绘制的宽度和高度。如果不说明，在绘制时image宽度和高度不会缩放。

#### 绘制

drawImage(image, dx, dy)：只有单纯的绘制功能，可以绘制图片、视频和别的Canvas对象等。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制 - drawImage</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
        var img = new Image();
        img.src = 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20191212%2F556cc408058d4c64a46468761406afe6.png&refer=http%3A%2F%2F5b0988e595225.cdn.sohucs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1660103116&t=8dd0c641e1e1890fa65ee80dfa428d34';
        img.onload = function(){
          ctx.drawImage(img, 0, 0);
        }
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/936ed533db894931b32f4b1c3a745f3b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

如上图所示，咱们可以简单地把一个图片绘制到Canvas中。但上面的效果却不是我们预期中的效果，那么如果我们想把图片完整的绘制到canvas中，我们就需要别的参数。

#### 缩放

drawImage(image, dx, dy, dWidth, dHeight)：在绘制的基础上我们又增加了两个参数，这两个参数能控制绘制元素的大小，整体实现一个缩放的效果。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制 - drawImage</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
        var img = new Image();
        img.src = 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20191212%2F556cc408058d4c64a46468761406afe6.png&refer=http%3A%2F%2F5b0988e595225.cdn.sohucs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1660103116&t=8dd0c641e1e1890fa65ee80dfa428d34';
        img.onload = function(){
          ctx.drawImage(img, 0, 0, 500, 500);
        }
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/710caf686a834b49902c5d125105eb42~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

这里需要说明一点，在设置dWidth和dHeight两个参数时，不能只设置其中一个，要么都设置要么都不设置。

#### 裁剪

drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)：在缩放的基础上又增加了四个参数，整体也是在缩放的基础上增加了裁剪的功能。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制 - drawImage</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var img = new Image();
      img.src = 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20191212%2F556cc408058d4c64a46468761406afe6.png&refer=http%3A%2F%2F5b0988e595225.cdn.sohucs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1660103116&t=8dd0c641e1e1890fa65ee80dfa428d34';
      img.onload = function(){
        // 在图片的（100，100）位置，裁剪一个300*300大小的内容，然后缩放到500*500绘制到Canvas中（0, 0）的地方
        ctx.drawImage(img, 100, 100, 300, 300, 0, 0, 500, 500);
      }
    }
  </script>
</body>
</html>
复制代码
```

上面代码其实就是：在原图片的（100，100）位置，裁剪一个300*300大小的内容，然后再缩放到500*500绘制到Canvas中（0, 0）的地方。

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3fbe3a5f5d704442a431d455cfb9e0fa~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

## 变形

变形算是canvas基础的进阶把，它是一种更强大的方法，可以将原点移动到另一点，还能对网格进行旋转和缩放。

### 状态的保存和恢复

save() 和 restore() 方法是用来保存和恢复 canvas 状态的，方法不需要参数。可以理解为就是对canvas 状态的快照进行保存和恢复。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制 - drawImage</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.fillStyle = "#cccccc";
      ctx.fillRect(10, 10, 300, 100);
      ctx.save(); // 保存状态
      ctx.fillStyle = "#ee7034";
      ctx.fillRect(10, 150, 300, 100);
      ctx.restore(); // 还原到上次保存的状态
      ctx.fillRect(10, 300, 300, 100);
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3d970372edee4584a2172e2c2f66eb8a~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

如上图效果我们可以看出，当我们保存一个状态以后，在我们恢复以后可以继续使用这个状态。

Canvas的状态是存储在栈中的，每次调用save()方法后，当前的状态都会被推送到栈中保存起来。

一个绘画状态包括：

- 应用的变形：移动、旋转、缩放、strokeStyle、fillStyle、globalAlpha、lineWidth、lineCap、lineJoin、miterLimit、lineDashOffset、shadowOffsetX、shadowOffsetY、shadowBlur、shadowColor、globalCompositeOperation、font、textAlign、textBaseline、direction、imageSmoothingEnabled等。
- 应用的裁切路径：clipping path

PS：保存和恢复可以多次调用， 需要注意的是每一次调用 restore 方法，上一个保存的状态就从栈中弹出，所有设定都恢复。

### 移动、旋转和缩放

- 移动：translate(x, y) ，x 是左右偏移量，y 是上下偏移量。
- 旋转：rotate(angle)，angle是旋转的角度，它是顺时针旋转，以弧度为单位的值。
- 缩放：scale(x, y)，x 为水平缩放的值，y 为垂直缩放得值。x和y的值小于1则为缩小，大于1则为放大。默认值为 1。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制 - drawImage</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.fillStyle = '#ee7034';
      ctx.save();
      ctx.save();
      ctx.translate(100, 100); // x和y轴都移动了100
      ctx.fillRect(0, 0, 100, 100);
      ctx.restore();
      ctx.rotate(Math.PI / 4); // 旋转了45度，Math.PI=180度
      ctx.fillRect(0, 0, 100, 100);
      ctx.restore();
      ctx.scale(2, 1);
      ctx.fillRect(100, 300, 100, 100);
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e86ec6c7db354e8280171c1bba5d4aee~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

PS：这里需要注意三点：
save()保存的状态是可以多次保存的，同时保存在栈中的元素遵循的是后进先出的顺序；
旋转的中心点始终是 canvas 的原点；
缩放如果是负值的话，则是一个镜像的效果。

### transform、setTransform、resetTransform

- transform(a, b, c, d, e, f)方法能将当前的变形矩阵乘上一个基于自身参数的矩阵；
- setTransform(a, b, c, d, e, f)方法会将当前变形矩阵重置为单位矩阵，然后用相同的参数调用 transform 方法
- resetTransform()方法为重置当前变形为单位矩阵。效果等同于调用 setTransform(1, 0, 0, 1, 0, 0)

需要注意的是transform方法和setTransform方法中如果任意一个参数是无限大（Infinity），那么变形矩阵也必须被标记为无限大，否则会抛出异常。

参数说明：

- a：水平方向的缩放
- b：竖直方向的倾斜偏移
- c：水平方向的倾斜偏移
- d：竖直方向的缩放
- e：水平方向的移动
- f：竖直方向的移动

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制 - drawImage</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      // 
      var sin = Math.sin(Math.PI / 6);
      var cos = Math.cos(Math.PI / 6);
      console.log("🚀 ~ cos, sin", cos, sin)
      ctx.translate(250, 250);
      var c = 0;
      for (var i=0; i <= 12; i++) {
        c = Math.floor(255 / 12 * i);
        ctx.fillStyle = `rgba(${c}, ${c}, ${c})`;
        ctx.beginPath() // 开启路径
        ctx.arc(60, 100, 100, 0, Math.PI*2, false);
        ctx.fill();
        ctx.transform(cos, sin, -sin, cos, 0, 0);
      }
      // 绘制一个矩形
      ctx.fillStyle = "rgba(255, 128, 255, 0.5)";
      ctx.fillRect(0, 50, 100, 100);
      // 上面绘制的矩形不是我们想要的没因为它带上了上面transform的属性
      // 所以需要重置当前变形为单位矩阵
      ctx.resetTransform()
      ctx.fillStyle = "rgba(255, 128, 255, 0.5)";
      ctx.fillRect(0, 50, 100, 100);
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f9fd1cf4586e4792857887aa6ef8973c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 合成与裁剪

### 合成

合成的图形受限于绘制的顺序。如果我们不想受限于绘制的顺序，那么我们可以利用 globalCompositeOperation 属性来改变这种情况。

语法：globalCompositeOperation = type，type为合成的类型，具体有哪些类型下面我们将分别看一下：

#### source-over

默认值，在现有画布上下文之上绘制新图形。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 绘制 - drawImage</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      ctx.beginPath();
      ctx.fillStyle = "rgba(255, 0, 0, 1)";
      ctx.fillRect(50, 100, 300, 150);
      ctx.globalCompositeOperation = 'source-over'
      ctx.beginPath();
      ctx.fillStyle = "rgba(0, 255, 0, 1)";
      ctx.fillRect(50, 150, 150, 250);
      ctx.globalCompositeOperation = 'source-over'
      ctx.beginPath();
      ctx.fillStyle = "rgba(0, 0, 255, 1)";
      ctx.fillRect(150, 200, 150, 150);
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ddb732b70768468293abcbf76d0ac6f7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### source-in

新图形只在新图形和目标画布重叠的地方绘制。其他的都是透明的。

```ini
 ctx.globalCompositeOperation = 'source-in'
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e09e4ceb1cf24b35be4febb90a5f9209~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### source-out

在不与现有画布内容重叠的地方绘制新图形。

```ini
ctx.globalCompositeOperation = 'source-out'
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0457eecc718b430f92b4c13bfb5df8c5~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### source-atop

新图形只在与现有画布内容重叠的地方绘制。

```ini
ctx.globalCompositeOperation = 'source-atop'
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ea00c088ccd4c1ba626f248ca2387a2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### destination-over

在现有的画布内容后面绘制新的图形。

```ini
ctx.globalCompositeOperation = 'destination-over'
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ffeb9d1e79724f92b6957f000d082a94~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### destination-in

现有的画布内容保持在新图形和现有画布内容重叠的位置。其他的都是透明的。

```ini
ctx.globalCompositeOperation = 'destination-in'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/34d4d75bce15477790ef1637eb981d13~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### destination-out

现有内容保持在新图形不重叠的地方。

```ini
ctx.globalCompositeOperation = 'destination-out'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a4fb2dae25c543ba889893d8a1a4ef0d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### destination-atop

现有的画布只保留与新图形重叠的部分，新的图形是在画布内容后面绘制的。

```ini
ctx.globalCompositeOperation = 'destination-atop'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aed285fc205b4bc99bb44a1e2d1175c2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### lighter

两个重叠图形的颜色是通过颜色值相加来确定的。

```ini
ctx.globalCompositeOperation = 'lighter'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04cca01309824e2bb15e21b233572cae~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### copy

只显示新图形。

```ini
ctx.globalCompositeOperation = 'copy'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/64197279c59b4fc49c0d6a4d1df68b4d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### xor

图像中，那些重叠和正常绘制之外的其他地方是透明的。

```ini
ctx.globalCompositeOperation = 'xor'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ff5632a26516427b94a3ce48687b7bd9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### multiply

将顶层像素与底层相应像素相乘，结果是一幅更黑暗的图片。

```ini
ctx.globalCompositeOperation = 'multiply'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a6192c99e6f44343afeb4704ce0fc890~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### screen

像素被倒转，相乘，再倒转，结果是一幅更明亮的图片。

```ini
ctx.globalCompositeOperation = 'screen'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d495ae1763964c3b87a87fc28cc5106a~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### overlay

multiply 和 screen 的结合，原本暗的地方更暗，原本亮的地方更亮。

```ini
ctx.globalCompositeOperation = 'overlay'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/15f47059101b438387c17db28947ae93~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### darken

保留两个图层中最暗的像素。

```ini
ctx.globalCompositeOperation = 'darken'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b465c94c4ea44c1d9aa48f40d6bd8d4a~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### lighten

保留两个图层中最亮的像素。

```ini
ctx.globalCompositeOperation = 'lighten'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a7cabc3accbc4b2a95aff35b5da06550~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### color-dodge

将底层除以顶层的反置。

```ini
ctx.globalCompositeOperation = 'color-dodge'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1a0fbd979c24db6bd23dacd1998ecf6~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### color-burn

将反置的底层除以顶层，然后将结果反过来。

```ini
ctx.globalCompositeOperation = 'color-burn'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/65e0efad646f4dfbbb07c35312bdaa4e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### hard-light

屏幕相乘（A combination of multiply and screen）类似于叠加，但上下图层互换了。

```ini
ctx.globalCompositeOperation = 'hard-light'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/919c5b118e2245e1900c6d9ab75761ef~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### soft-light

用顶层减去底层或者相反来得到一个正值。

```ini
ctx.globalCompositeOperation = 'soft-light'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/baf87750eea8453fbe2bc109564581dc~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### difference

一个柔和版本的强光（hard-light）。纯黑或纯白不会导致纯黑或纯白。

```ini
ctx.globalCompositeOperation = 'difference'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2c1410e91e6c4d19997b8c895fcf39aa~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### exclusion

和 difference 相似，但对比度较低。

```ini
ctx.globalCompositeOperation = 'exclusion'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e7adbfdf87b5494ab589049f5d64fdb0~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### hue

保留了底层的亮度（luma）和色度（chroma），同时采用了顶层的色调（hue）。

```ini
ctx.globalCompositeOperation = 'hue'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/43ffc132aed1419b90dbba6a92eb61c9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### saturation

保留底层的亮度（luma）和色调（hue），同时采用顶层的色度（chroma）。

```ini
ctx.globalCompositeOperation = 'saturation'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bdf331e38dfe49ccb92502f5f958ced9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### color

保留了底层的亮度（luma），同时采用了顶层的色调 (hue) 和色度 (chroma)。

```ini
ctx.globalCompositeOperation = 'color'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bf5318a89b6f4c89a5533b8ae383b81e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### luminosity

保持底层的色调（hue）和色度（chroma），同时采用顶层的亮度（luma）。

```ini
ctx.globalCompositeOperation = 'luminosity'
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/05e01e5698ef4de1bfb76060ece331a9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 裁剪

裁剪的作用是遮罩，用来隐藏不需要的部分，所有在路径以外的部分都不会在 canvas 上绘制出来。

裁剪的效果和 globalCompositeOperation 属性的 source-in 和 source-atop差不多，但也有区别，最重要的区别是裁剪路径不会在 canvas 上绘制东西，而且它永远不受新图形的影响。这些特性使得它在特定区域里绘制图形时特别好用。

语法：clip() 将当前正在构建的路径转换为当前的裁剪路径。

默认情况下，canvas 有一个与它自身一样大的裁剪路径（也就是没有裁剪效果）。现在可以通过clip()来创建一个裁剪路劲（也就有裁剪效果了）。

#### clip()

直接举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 裁剪</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
        var img = new Image();
        img.src = 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20191212%2F556cc408058d4c64a46468761406afe6.png&refer=http%3A%2F%2F5b0988e595225.cdn.sohucs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1660103116&t=8dd0c641e1e1890fa65ee80dfa428d34';
        img.onload = function(){
          // 创建圆形裁剪路径
          ctx.arc(250, 250, 200, 0, Math.PI*2, false);
          ctx.clip();
          // 创建完后绘制
          ctx.drawImage(img, 0, 0, 500, 500);
        }
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dde455c0eb5b48c2a104b3d8083de9fe~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### clip(path, fillRule)

- path为需要剪切的 Path2D 路径
- fillRule为判断是在路径内还是在路径外，允许的值有 nonzero（默认值）：非零环绕原则，evenodd：奇偶环绕原则

##### Path2D

Path2D 用来声明路径，语法：Path2D()，它是一个构造函数，可以创建一个新的 Path2D 对象。
Path2D()有不少方法，先了解一下：

- addPath()：添加一条新路径到对当前路径。
- closePath()：使笔点返回到当前子路径的起始点。它尝试从当前点到起始点绘制一条直线。 如果图形已经是封闭的或者只有一个点，那么此函数不会做任何操作。
- moveTo()：将一个新的子路径的起始点移动到 (x，y) 坐标。
- lineTo()：使用直线连接子路径的终点到 x, y 坐标。
- bezierCurveTo()：添加一条三次贝赛尔曲线到当前路径。 该方法需要三个点。 第一、第二个点是控制点，第三个点是结束点。起始点是当前路径的最后一个点，绘制贝赛尔曲线前，可以通过调用 moveTo() 进行修改。
- quadraticCurveTo()：添加一条二次贝赛尔曲线到当前路径。
- arc()：添加一条圆弧路径。 圆弧路径的圆心在 (x, y) 位置，半径为 r ，根据anticlockwise （默认为顺时针）指定的方向从 startAngle 开始绘制，到 endAngle 结束。
- arcTo()：根据控制点和半径添加一条圆弧路径，使用直线连接前一个点。
- ellipse()：添加一条椭圆路径。椭圆的圆心在（x,y）位置，半径分别是radiusX 和 radiusY ，按照anticlockwise （默认顺时针）指定的方向，从 startAngle 开始绘制，到 endAngle 结束。
- rect()：创建一条矩形路径，矩形的起点位置是 (x, y) ，尺寸为 width 和 height。

下面举例看一下如何应用 Path2D 来创建一个裁剪路径：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 裁剪</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
        var img = new Image();
        img.src = 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20191212%2F556cc408058d4c64a46468761406afe6.png&refer=http%3A%2F%2F5b0988e595225.cdn.sohucs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1660103116&t=8dd0c641e1e1890fa65ee80dfa428d34';
        img.onload = function(){
          // 创建圆形裁剪路径
          ctx.arc(250, 250, 200, 0, Math.PI*2, false);
          var path1 = new Path2D();
          path1.rect(100, 100, 300, 300);
          ctx.clip(path1);
          // 创建完后绘制
          ctx.drawImage(img, 0, 0, 500, 500);
        }
    }
  </script>
</body>
</html>
复制代码
```

## 动画

在 canvas 上绘制内容是用 canvas 提供的或者自定义的方法，而通常我们仅仅在脚本执行结束后才能看见结果，所以想在 for 循环里面完成动画是不可能的。那么为了实现动画，我们需要一些可以定时执行重绘的方法。

- setInterval(function, delay) ：定时器，当设定好间隔时间后，function 会定期执行。
- setTimeout(function, delay)：延时器，在设定好的时间之后执行函数
- requestAnimationFrame(callback)：告诉浏览器你希望执行一个动画，并在重绘之前，请求浏览器执行一个特定的函数来更新动画。

如果不需要与用户互动，可以使用 setInterval() 方法，它可以定期执行指定的代码。如果需要做游戏，可以使用键盘或者鼠标事件配合上 setTimeout() 方法来实现。通过设置事件监听，可以捕捉用户的交互，并执行相应的动作。

下面我们采用 window.requestAnimationFrame()来实现一个动画效果。requestAnimationFrame()方法提供了更加平缓且有效率的方式来执行动画，当系统准备好重绘条件后才会调用绘制动画帧。一般每秒钟回调函数执行 60 次，也有可能会被降低，因为通常情况下requestAnimationFrame()方法会遵循 W3C 的建议，浏览器中的回调函数执行次数通常与浏览器屏幕刷新次数相匹配。还有为了提高性能和电池寿命，通常 requestAnimationFrame() 方法运行在后台标签页或者隐藏在 里时，requestAnimationFrame() 方法会暂停调用以提升性能和电池寿命。

举个例子看一下：

```html
    <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 太阳系</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var sun = new Image();
      var moon = new Image();
      var earth = new Image();
      function init(){
        sun.src = 'https://img.lovepik.com/element/40097/4339.png_300.png';
        moon.src = 'https://www.freepnglogos.com/uploads/moon-png/moon-png-annual-celestial-overview-simone-matthews-18.png';
        earth.src = 'https://gd-filems.dancf.com/mcm79j/mcm79j/92054/b3162056-61ba-4ebd-8da1-fd98ce15a1cb31401764.png';
        window.requestAnimationFrame(draw);
      }
      function draw() {
        var ctx = document.getElementById('canvas').getContext('2d');
        ctx.globalCompositeOperation = 'destination-over';
        // 清空画布
        ctx.clearRect(0, 0, 500, 500);
        ctx.fillStyle = 'rgba(0, 0, 0, 0.4)';
        ctx.strokeStyle = 'rgba(0, 153, 255, 0.4)';
        ctx.save(); // 第一次保存画布状态
        ctx.translate(250, 250); // 把原心移到画布中间
        // 画一个地球
        var time = new Date();
        var earthDeg =  ((2 * Math.PI) / 60) * time.getSeconds() + ((2 * Math.PI) / 60000) * time.getMilliseconds()
        ctx.rotate(earthDeg);
        ctx.translate(200, 0);
        ctx.drawImage(earth, -20, -20, 40, 40);
        // 画一个月亮
        ctx.save(); // 第二次保存画布状态
        var moonDeg = ((2 * Math.PI) / 6) * time.getSeconds() + ((2 * Math.PI) / 6000) * time.getMilliseconds() 
        ctx.rotate(moonDeg);
        ctx.translate(0, 40);
        ctx.drawImage(moon, -7.5, -7.5, 15, 15);
        // 恢复状态
        ctx.restore(); 
        ctx.restore();
        // 画一个地球运行的轨迹
        ctx.beginPath();
        ctx.arc(250, 250, 200, 0, Math.PI * 2, false);
        ctx.stroke();
        // 画一个太阳
        ctx.drawImage(sun, 0, 0, 500, 500);
        window.requestAnimationFrame(draw);
      }
      init();
    }
  </script>
</body>
</html>

复制代码
```

效果如下：

![未标题-1.gif](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4bb365efbf6d432887e6a2f08d79cd5c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

总结一下绘制动画的基本步骤

- 清空 canvas：除非接下来要画的内容会完全充满 canvas（例如背景图），否则需要清空所有。最简单的做法就是用 clearRect 方法。
- 保存 canvas 状态：如果要改变 canvas 状态的设置（样式，变形之类的），之后又要在每画一帧之时都是原始状态的情况时，需要先保存一下。
- 绘制动画图形（animated shapes）
- 恢复 canvas 状态：如果已经保存了 canvas 的状态，可以先恢复它，然后重绘下一帧。

## 高级动画

高级动画就是在初级动画的基础上加上一些符合物理的运动，这样就能使我们的动画更生动而不是那么的呆板。
下面我们一步步来实现一个小球的自由落体的运动。

### 绘制小球

首先我们先绘制一个小球，直接上代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 动画</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var ball = {
        x: 100,
        y: 100,
        radius: 25,
        color: 'blue',
        draw: function() {
          ctx.beginPath();
          ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
          ctx.closePath();
          ctx.fillStyle = this.color;
          ctx.fill();
        }
      };
      ball.draw();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/153ebb63d4cf486e8b9add00bb3017d5~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 速率

我们通过给小球添加速率矢量进行移动。这个依旧用requestAnimationFrame() 方法来实现，在每一帧里面，依旧用clear 清理掉之前帧里旧的圆形。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 裁剪</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var ball = {
        x: 100,
        y: 100,
        vx: 1,
        vy: 3,
        radius: 25,
        color: 'blue',
        draw: function() {
          ctx.beginPath();
          ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
          ctx.closePath();
          ctx.fillStyle = this.color;
          ctx.fill();
        }
      };
      function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ball.draw();
        // 添加速率
        ball.x += ball.vx;
        ball.y += ball.vy;
       window.requestAnimationFrame(draw);
      }
      window.requestAnimationFrame(draw);
      ball.draw();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ef52a334d214b31a842d2662267cae0~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 边界

想让小球反弹那么我们就需要添加边界

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 裁剪</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var ball = {
        x: 100,
        y: 100,
        vx: 1,
        vy: 3,
        radius: 25,
        color: 'blue',
        draw: function() {
          ctx.beginPath();
          ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
          ctx.closePath();
          ctx.fillStyle = this.color;
          ctx.fill();
        }
      };
      function draw() {
        ctx.clearRect(0,0, canvas.width, canvas.height);
        ball.draw();
        // 添加速率
        ball.x += ball.vx;
        ball.y += ball.vy;
        // 添加边界
        if (ball.y + ball.vy > canvas.height || ball.y + ball.vy < 0) {
          ball.vy = -ball.vy;
        }
        if (ball.x + ball.vx > canvas.width || ball.x + ball.vx < 0) {
          ball.vx = -ball.vx;
        }
       window.requestAnimationFrame(draw);
      }
      window.requestAnimationFrame(draw);
      ball.draw();
    }
  </script>
</body>
</html>
复制代码
```

添加完边界的效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ba1d17997aa439d801407c58b9bc1ef~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 加速度

为了让动作更真实，我们还需要加入加速度的处理。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 裁剪</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var ball = {
        x: 100,
        y: 100,
        vx: 1,
        vy: 3,
        radius: 25,
        color: 'blue',
        draw: function() {
          ctx.beginPath();
          ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
          ctx.closePath();
          ctx.fillStyle = this.color;
          ctx.fill();
        }
      };
      function draw() {
        ctx.clearRect(0,0, canvas.width, canvas.height);
        ball.draw();
        // 添加加速度
        ball.vy *= .99;
        ball.vy += .25;
        // 添加速率
        ball.x += ball.vx;
        ball.y += ball.vy;
        // 添加边界
        if (ball.y + ball.vy > canvas.height || ball.y + ball.vy < 0) {
          ball.vy = -ball.vy;
        }
        if (ball.x + ball.vx > canvas.width || ball.x + ball.vx < 0) {
          ball.vx = -ball.vx;
        }
       window.requestAnimationFrame(draw);
      }
      window.requestAnimationFrame(draw);
      ball.draw();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/919d64c7eda34716bf7e683ca6ad4ba4~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 拖尾效果

加一个拖尾效果：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 裁剪</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var ball = {
        x: 100,
        y: 100,
        vx: 1,
        vy: 3,
        radius: 25,
        color: 'blue',
        draw: function() {
          ctx.beginPath();
          ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
          ctx.closePath();
          ctx.fillStyle = this.color;
          ctx.fill();
        }
      };
      function draw() {
        // ctx.clearRect(0, 0, canvas.width, canvas.height);
        // 用带透明度的矩形代替清空
        ctx.fillStyle = 'rgba(255, 255, 255, 0.3)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ball.draw();
        // 添加加速度
        ball.vy *= .995;
        ball.vy += .15;
        // 添加速率
        ball.x += ball.vx;
        ball.y += ball.vy;
        // 添加边界
        if (ball.y + ball.vy > canvas.height || ball.y + ball.vy < 0) {
          ball.vy = -ball.vy;
        }
        if (ball.x + ball.vx > canvas.width || ball.x + ball.vx < 0) {
          ball.vx = -ball.vx;
        }
       window.requestAnimationFrame(draw);
      }
      window.requestAnimationFrame(draw);
      ball.draw();
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3b66638cdf404beabbb299d3980b79de~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

## 应用

最后说一下Canvas的应用。首先是可视化数据的应用，比如说：百度的ECharts、阿里的G2等图表可视化插件。其次是游戏的应用，Canvas 在基于Web的图像显示方面比 Flash 更加立体、精巧，且Canvas游戏在流畅度和跨平台方面更牛。还有图形编辑器的应用，比如可视化组态编辑工具HT，它完全基于Canvas绘制。未来Photoshop能够基于Canvas在web端100%实现。最后模拟器的应用，模拟器产品可以完全由 canvas 来实现，视觉效果上更加逼真模拟实际生活中的工具，进一步优化物联网环境下的使用体验，降低使用者的学习成本。总是未来Canvas的应用只会越来越普遍，所以具备Canvas这门技术是必不可缺的。

今天呢咱们不可能讲解如何用Canvas开发一个游戏或者如何实现一个Photoshop。今天只能说一些平常咱们开发中常用的小应用，比如：保存图片、图片灰度或者反相颜色等。

### 保存图片

保存图片主要应用的方法是：toDataURL()

语法：canvas.toDataURL('image/png')，默认设定创建一个 PNG 图片。

canvas.toDataURL('image/jpeg', quality)，也可以设定为创建一个 JPG 图片。还能有选择地提供从 0 到 1 的品质量，1 表示最好品质，0 表示品质最差基本无法辨别。

当我们用toDataURL()方法从画布中生成了一个数据链接后，我们可以将它用于![img](https://juejin.cn/post/7119495608938790942)元素显示出来，也可以将它放在一个有 download 属性的超链接里用于保存到本地。

举个例子看一下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 保存图片</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
      float: left;
    }
    img {
      width: 500px;
      height: 500px;
      float: right;
    }
    button {
      position: absolute;
      top: 550px;
      left: 50%;
      margin-left: -40px;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <img id="img" src="https://t7.baidu.com/it/u=1819248061,230866778&fm=193&f=GIF" />
  <button id="btn">转化为图片且下载</button>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    var Img = document.getElementById('img');
    var Btn = document.getElementById('btn');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var ball = {
        x: 100,
        y: 100,
        vx: 1,
        vy: 3,
        radius: 25,
        color: 'blue',
        draw: function() {
          ctx.beginPath();
          ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
          ctx.closePath();
          ctx.fillStyle = this.color;
          ctx.fill();
        }
      };
      function draw() {
        // ctx.clearRect(0, 0, canvas.width, canvas.height);
        // 用带透明度的矩形代替清空
        ctx.fillStyle = 'rgba(255, 255, 255, 0.3)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ball.draw();
        // 添加加速度
        ball.vy *= .995;
        ball.vy += .15;
        // 添加速率
        ball.x += ball.vx;
        ball.y += ball.vy;
        // 添加边界
        if (ball.y + ball.vy > canvas.height || ball.y + ball.vy < 0) {
          ball.vy = -ball.vy;
        }
        if (ball.x + ball.vx > canvas.width || ball.x + ball.vx < 0) {
          ball.vx = -ball.vx;
        }
       window.requestAnimationFrame(draw);
      }
      window.requestAnimationFrame(draw);
      ball.draw();
    }
    Btn.addEventListener('click', function(){
      // 将canvas转换成base64的url
      var url = canvas.toDataURL("image/png"); 
      // 把Canvas 转化为图片
      Img.src = url;
      // 将base64转换为文件对象
      var arr = url.split(",")
      var mime = arr[0].match(/:(.*?);/)[1] // 此处得到的为文件类型
      var bstr = atob(arr[1]) // 此处将base64解码
      var n = bstr.length
      var u8arr = new Uint8Array(n);
      while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
      }
      // 通过以下方式将以上变量生成文件对象，三个参数分别为文件内容、文件名、文件类型
      var file = new File([u8arr], "filename", { type: mime });
      // 将文件对象通过a标签下载
      var aDom = document.createElement("a"); // 创建一个 a 标签
      aDom.download = file.name; // 设置文件名
      let href = URL.createObjectURL(file); // 将file对象转成 UTF-16 字符串
      aDom.href = href; // 放入href
      document.body.appendChild(aDom); // 将a标签插入 body
      aDom.click(); // 触发 a 标签的点击
      document.body.removeChild(aDom); // 移除刚才插入的 a 标签
      URL.revokeObjectURL(href); // 释放刚才生成的 UTF-16 字符串
    });
  </script>
</body>
</html>
复制代码
```

效果如下：![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d18f27e3874f4c1d8291313e6d48add6~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 反相颜色

我们可以遍历所有像素然后改变他们的数值，然后将被修改的像素数组通过 putImageData() 方法放回到画布中去，以达到反相颜色。invert 函数仅仅是去减掉颜色的最大色值 255，grayscale 函数仅仅是用红绿和蓝的平均值。也可以用加权平均，例如 x = 0.299r + 0.587g + 0.114b 这个公式。

直接上例子吧：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 保存图片</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
      float: left;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="1000" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <button id="original">Original</button>
  <button id="grayscale">Grayscale</button>
  <button id="inverted">Inverted</button>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    var originalEl = document.getElementById('original');
    var grayscaleEl = document.getElementById('grayscale');
    var invertedEl = document.getElementById('inverted');
    var sepiaEl = document.getElementById('sepia');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var img = new Image();
      img.crossOrigin = 'anonymous';
      img.src = 'https://t7.baidu.com/it/u=1819248061,230866778&fm=193&f=GIF';
      img.onload = function() {
        ctx.drawImage(img, 0, 0);
      };
      var original = function() {
        ctx.drawImage(img, 0, 0);
      };
      var invert = function() {
        ctx.drawImage(img, 0, 0);
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const data = imageData.data;
        for (var i = 0; i < data.length; i += 4) {
          data[i]     = 255 - data[i];     // red
          data[i + 1] = 255 - data[i + 1]; // green
          data[i + 2] = 255 - data[i + 2]; // blue
        }
        ctx.putImageData(imageData, 0, 0);
      };

      var grayscale = function() {
        ctx.drawImage(img, 0, 0);
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const data = imageData.data;
        for (var i = 0; i < data.length; i += 4) {
          var avg = (data[i] + data[i + 1] + data[i + 2]) / 3;
          data[i]     = avg; // red
          data[i + 1] = avg; // green
          data[i + 2] = avg; // blue
        }
        ctx.putImageData(imageData, 0, 0);
      };
      originalEl.addEventListener("click", function(evt) {
        original()
      })
      grayscaleEl.addEventListener("click", function(evt) {
        grayscale()
      })
      invertedEl.addEventListener("click", function(evt) {
        invert()
      })
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7150d83350e4e24b790b44b2dad3622~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 像素数据

上面的场景中我们使用了一个方法：getImageData()。该方法会返回一个 ImageData对象，它是画布区域的数据，画布的四个角分别表示为 (left, top)、(left + width, top)、(left, top + height)和(left + width, top + height) 四个点。这四个坐标点被设定为画布坐标空间元素。

语法：ctx.getImageData(left, top, width, height)

接来下我们再用这个方法做一个拾色器

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>canvas - 拾色器</title>
  <style>
    /* 给画布增加一个阴影和圆角的样式 */
    canvas {
      box-shadow: 0px 0px 5px #ccc;
      border-radius: 8px;
      float: left;
    }
    div {
      width: 200px;
      height: 500px;
      float: left;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500">
    当前浏览器不支持canvas元素，请升级或更换浏览器！
  </canvas>
  <div id="hovered"></div>
  <div id="selected"></div>
  <script>
    // 获取 canvas 元素
    var canvas = document.getElementById('canvas');
    var originalEl = document.getElementById('original');
    var grayscaleEl = document.getElementById('grayscale');
    var invertedEl = document.getElementById('inverted');
    var sepiaEl = document.getElementById('sepia');
    // 通过判断getContext方法是否存在来判断浏览器的支持性
    if(canvas.getContext) {
      // 获取绘图上下文
      var ctx = canvas.getContext('2d');
      var img = new Image();
      img.crossOrigin = 'anonymous';
      img.src = 'https://t7.baidu.com/it/u=1819248061,230866778&fm=193&f=GIF';
      img.onload = function() {
        ctx.drawImage(img, 0, 0);
        img.style.display = 'none';
      };
      var hoveredColor = document.getElementById('hovered');
      var selectedColor = document.getElementById('selected');
      function pickColor(type, event, destination) {
        var x = event.layerX;
        var y = event.layerY;
        var pixel = ctx.getImageData(x, y, 1, 1);
        var data = pixel.data;
        const rgba = `rgba(${data[0]}, ${data[1]}, ${data[2]}, ${data[3] / 255})`;
        destination.style.background = rgba;
        if(type === 'move') {
          destination.textContent = "划过的颜色为：" + rgba;
        } else {
          destination.textContent = "选中的颜色为：" + rgba;
        }
        return rgba;
      }
      canvas.addEventListener('mousemove', function(event) {
          pickColor('move', event, hoveredColor);
      });
      canvas.addEventListener('click', function(event) {
          pickColor('click', event, selectedColor);
      });
    }
  </script>
</body>
</html>
复制代码
```

效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b6fdd1eab919480dbb2a350a8b2ffc92~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

# 结语

Canvas在刚推出时主打的优势就是更快的渲染速度，刷新了人们对Web页面元素绘制速度的印象，但Canvas的优势却不仅限于此。随着技术的不断更新Canvas的应用也越来越广泛，各种可视化图标、游戏和各种图形化编辑器都把Canvas突显的淋漓尽致，尤其Google Docs已经宣布将会把HTML迁移到基于Canvas渲染，这一消息的出现又把Canvas推上了一个新的高度。

总之Canvas只会越来越重要，它一定会成为每个前端工程师必备的技能之一。所以学会它掌握它让你的技能图谱再添一员猛将。



原文地址： https://juejin.cn/post/7119495608938790942