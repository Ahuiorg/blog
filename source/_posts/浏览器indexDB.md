---
title: 浏览器indexDB
abbrlink: 5feabfc2
date: 2024-03-07 19:59:05
tags:
---

## 前言

[IndexDB](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FIndexedDB_API)是一个相对底层一点API。通常用于存储大量结构化数据，包括文件、Blob等。IndexDB的API使用索引来实现对数据的高性能读取。

localStorage、sessionStorage是浏览器最常用的数据存储方案。两个各有优缺点。

- [localStorage](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWindow%2FlocalStorage)支持持久性存储，浏览器会话结束依旧可以存储数据在浏览器内存中，方便下次数据读取与使用。但缺点是存储的大小有限制，不同的浏览器基于的存储空间有所区别，但是都在5M左右。对于普通的业务场景足够胜任。但是对于通讯应用，存储对话记录这种高密度数据存储的场景来说，内存空间完全是不够用的。
- [sessionStorage](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWindow%2FsessionStorage)支持会话存储，浏览器相同域名会话窗口之间支持内存数据共享。当会话结束之后，关闭会话窗口，内存数据会被清除。缺点很明显，会话不支持永久存储，同时内存空间也很小。

三者都是基于源（Origin）的对象存储。源（Origin）指的是协议、域名、端口。不同的源数据是不支持共享的，相互之间保持独立，能够完美的避免不同源的数据污染。

`www.baidu.com`和`www.google.com`两个是不同的源，数据均不支持共享。

如果不同源数据支持共享会造成严重的数据污染，如下面的场景：

`www.baidu.com`源存储了一个数据 键为a，值为b

`www.google.com`源存储了一个数据 键为a，值为b

两键值对修改会造成相互相应，从而引发数据污染。

## IndexDB优势

两者最显著的缺点是

1. 存储空间太小
2. 键值对总是以字符串形式存储，不支持其他数据格式，如Blob、ArrayBuffer
3. 不支持异步操作，数据量大时进行I/O操作性能体验差

IndexDb成为了如上问题的解决方案。

### 空间大小

IndexDB存储空间根据不同的设备而定，因为不同设备存储空间大小不一致，浏览器的最大存储空间是动态的，它完全取决于你的磁盘大小。**全局限制**为可用磁盘空间的 50％。在 Firefox 中，一个名为 Quota Manager 的内部浏览器工具会跟踪每个源用尽的磁盘空间，并在必要时删除数据。

因此，如果你的硬盘驱动器是 500GB，那么浏览器的总存储容量为 250GB。如果超过此范围，则会发起称为**源回收**的过程，删除整个源的数据，直到存储量再次低于限制。删除源数据没有只删一部分的说法——因为这样可能会导致不一致的问题。

另外一个是当存储空间达到上限之后会执行LRU策略，当可用磁盘空间已满时，配额管理器将根据 LRU 策略开始清除数据——最近最少使用的源将首先被删除，然后是下一个，直到浏览器不再超过限制。

### 异步执行

IndexDB执行的操作都是异步完成的，以免阻塞应用程序。

由于存储不涉及到浏览器的Document操作，因此我们还可以配合Web Worker实现数据交互。把IndexDB的操作都放在Web Worker中，大大提升性能。

## 使用方法

下面来介绍一下如何在Vue项目中使用IndexDB，我们实现两套方案，第一种是基于Hook模式，第二种是基于Class模式。

这样能够很好的应用到Vue2/3中（React同理）。

### 基于Hook

使用数据库首先需要进行数据库注册，注册成功之后，才可以进行数据的事务操作。

```typescript

import { Toast } from "vant";

interface Result {
  name: string;
  content: string;
}

interface Get {
  result: Result;
  onsuccess: () => void;
  onerror: (reason?: any) => void;
}

interface Put {
  onsuccess: (target: { result: Result }) => void;
  onerror: (reason?: any) => void;
}

const SQL_NAME = "yuyishijia-h5";
const DB_NAME = "h5";
let request: any;
let db: any;

// 初始化数据库
function init() {
  return new Promise((resolve, reject) => {
    request = window.indexedDB.open(SQL_NAME);
    request.onerror = (event: any) => {
      reject(event);
      Toast("缓存获取失败");
    };
    request.onsuccess = (event: any) => {
      resolve(event.target.result);
      db = event.target.result;
    };
    request.onupgradeneeded = (event: any) => {
      db = event.target.result;
      let objectStore;
      if (!db.objectStoreNames.contains(DB_NAME)) {
        // 创建数据库
        objectStore = db.createObjectStore(DB_NAME, {
          keyPath: "name",
          unique: true, // 使用name名称作为主键，且不允许重复
        });
        objectStore.createIndex("content", "content", { unique: false }); // 建立索引
      }
    };
  });
}

// get操作，读取数据
export async function get(name: string): Promise<Result> {
  return new Promise<Result>((resolve, reject) => {
  const select: Get = db
    .transaction([DB_NAME], "readonly")
    .objectStore(DB_NAME)
    .get(name);

  select.onsuccess = function () {
    resolve(select.result);
  };
  select.onerror = reject;
});
}

// add操作，添加数据
export async function add(name: string, content: string): Promise<Result> {
  return new Promise<Result>((resolve, reject) => {
  const select: Put = db
    .transaction([DB_NAME], "readwrite")
    .objectStore(DB_NAME)
    .add({ name: name, content });

  select.onsuccess = (event: any) => {
    resolve(event.target.result);
  };
  select.onerror = reject;
});
}

// update操作，更新数据
export async function update(name: string, content: string): Promise<Result> {
  return new Promise((resolve, reject) => {
  const select: Put = db
    .transaction([DB_NAME], "readwrite")
    .objectStore(DB_NAME)
    .put({ name, content });

  select.onsuccess = (event: any) => {
    resolve(event.target.result);
  };
  select.onerror = reject;
});
}

// remove操作，删除数据
export async function remove(name: string): Promise<Result> {
  return new Promise((resolve, reject) => {
  const select: Put = db
    .transaction([DB_NAME], "readwrite")
    .objectStore(DB_NAME)
    .delete(name);

  select.onsuccess = (event: any) => {
    resolve(event.target.result);
  };
  select.onerror = reject;
});
}

export default {
  init,
  add,
  get,
  update,
  remove,
};
```

在使用IndexDB之前需要先在入口函数注册数据库。

```typescript

import { createApp } from "vue";
import indexDB from "@/utils/storage";

await indexDB.init(); // 在入口函数初始化
const app = createApp(App);
app.mount("#app");
```

注册成功后，可以在业务中对数据库表进行增删改查操作

```typescript

<template>
  <div>使用IndexDB</div>
</template>

<script setup>
import IndexDB from "@/utils/storage";

// 加载数据
const loadData = async () => {
  let cache: any;
  try {
    cache = await IndexDB.get('主键名称');
  } catch (error) {
    console.log(error);
  }
};
</script>
```

### 基于Class

```typescript

import { Toast } from "vant";

const SQL_NAME = "yuyishijia-h5";
const DB_NAME = "h5";

class indexDb {
  name: string; // 主键
  request: any;
  db: any;
  constructor(name: string) {
    this.name = name;
  }

  init() {
    return new Promise((resolve, reject) => {
      this.request = window.indexedDB.open(SQL_NAME);
      this.request.onerror = (event: any) => {
        reject(event);
        Toast("缓存获取失败");
      };
      this.request.onsuccess = (event: any) => {
        resolve(event.target.result);
        this.db = event.target.result;
      };
      this.request.onupgradeneeded = (event: any) => {
        this.db = event.target.result;
        let objectStore;
        if (!this.db.objectStoreNames.contains(DB_NAME)) {
          objectStore = this.db.createObjectStore(DB_NAME, {
            keyPath: "name",
            unique: true, // 名称作为主键不允许重复
          });
          objectStore.createIndex("content", "content", { unique: false });
        }
      };
    });
  }

  async get(name: string) {
    return new Promise((resolve, reject) => {
      const transaction = this.db.transaction([DB_NAME], "readonly");
      const objectStore = transaction.objectStore(DB_NAME);
      const req = objectStore.get(name);

      req.onsuccess = function () {
        resolve(req.result);
      };
      req.onerror = reject;
    });
  }

  async add(content: string) {
    return new Promise((resolve, reject) => {
      const select = this.db
        .transaction([DB_NAME], "readwrite")
        .objectStore(DB_NAME)
        .add({ name: this.name, content });

      select.onsuccess = (event: any) => {
        resolve(event.target.result);
      };
      select.onerror = reject;
    });
  }

  async update(name: string, content: string) {
    return new Promise((resolve, reject) => {
      const select = this.db
        .transaction([DB_NAME], "readwrite")
        .objectStore(DB_NAME)
        .put({ name, content });

      select.onsuccess = (event: any) => {
        resolve(event.target.result);
      };
      select.onerror = reject;
    });
  }
}

export default indexDb;
```

class模式推荐配合pinia（vuex）做全局数据存储。

```typescript

import { defineStore } from 'pinia'
import indexDB from "@/utils/storage";

export const useDbStore = defineStore('db', () => {
    const db = new indexDB()
    db.init() // 初始化
    
    
    return { add: db.add, update: db.update, remove: db.remove, get: db.get } 
 })
```

标签：

[JavaScript]()[IndexedDB]()[Vue.js]()



作者：smallzip
链接：https://juejin.cn/post/7303475295028297767
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
