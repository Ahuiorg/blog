---
title: fix
abbrlink: 1509574496
date: 2022-07-15 23:13:23
tag: TypeScript
---

如何在TypeScript中的`window`上显式设置新属性？

要保持动态，只需使用：

(<any>window).MyNamespace