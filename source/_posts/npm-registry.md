---
title: npm registry
abbrlink: 4af6e48a
date: 2023-02-21 16:50:50
tags:
---

临时修改npm镜像：

```
npm i --registry https://registry.npmjs.org/ 
```

```
npm i --registry http://jfrog.cloud.qiyi.domain/api/npm/npm/
```

```
npm i --registry http://qui.qiyi.domain/npm
```



永久修改npm镜像：

```
npm config set registry https://registry.npm.taobao.org
```



重置npm镜像：

```
npm config set registry https://registry.npmjs.org/ 
```



查看镜像的配置结果：

```
npm config get registry
```


