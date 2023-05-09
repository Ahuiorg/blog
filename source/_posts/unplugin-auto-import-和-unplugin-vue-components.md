---
title: unplugin-auto-import 和 unplugin-vue-components
abbrlink: 88f781f4
date: 2023-02-24 15:50:35
tags:
---

# unplugin-auto-import 和 unplugin-vue-components

[![img](https://p3-passport.byteimg.com/img/user-avatar/d90a86ff79c931122ecfcd9a0c9b6a42~100x100.awebp)](https://juejin.cn/user/1943592288391496)

[Bigger![lv-4](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/img/lv-4.a78c420.png)![img](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/07302452a7ad81cb43a173b5cd580237.svg)](https://juejin.cn/user/1943592288391496)

2022年11月24日 18:15 · 阅读 1780

![unplugin-auto-import 和 unplugin-vue-components](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2113b5e3e7ff475aa51cd19ac102d42b~tplv-k3u1fbpfcp-zoom-crop-mark:3024:3024:3024:1702.awebp?)

***本文正在参加[「金石计划 . 瓜分6万现金大奖」](https://juejin.cn/post/7162096952883019783)***

## 背景

**unplugin-auto-import**：为 Vite、Webpack、Rollup 和 esbuild **按需自动导入 API**。支持 TypeScript。

**unplugin-vue-components**：Vue 的**按需组件自动导入**。

这两个插件都是涉及到**按需自动导入**，所以我们在使用 Vue 和其对应的 组件之类时，都可能会需要这两个插件的帮助，帮助我们实现**按需自动导入**，避免全量引入的尴尬以及每个文件都要手动导入 API 的低效重复搬砖。

但是，在项目中使用 unplugin-auto-import 和 unplugin-vue-components 总会遇到的一些问题，在此特意汇总如下，以及提供最后的解决办法，希望帮助到有需要的人。

## 安装

首先就是安装，为啥推荐使用 pnpm ，在此就不赘述了（可自行去了解）。

```sh
pnpm add -D unplugin-auto-import

pnpm add -D unplugin-vue-components
复制代码
```

## vite 版本

修改 vite.config.ts 文件内容，在此以 ElementPlusResolver 为例，其他组件类同。

```ts
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

AutoImport({
  imports: ["vue", "vue-router"],
  resolvers: [ElementPlusResolver()],
}),
Components({
    resolvers: [ElementPlusResolver()],
}),
复制代码
```

### 问题1：自动导入的依然 eslint 报错

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed88438194c4481bad3e02fe67516eb3~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

**现象**：使用过程中会自动引入 Vue 相关组合 Api，是起作用的，但是 eslint 却报错，让人很不舒服。

**分析**：起作用表示导入是正常可以用的，那么就是 eslint 的问题。但是怎么解决呢？是不是半天苦苦无果？

**解决办法**：

在刚才的 vite.config.ts 文件中修改：

```ts
AutoImport({
  imports: ["vue", "vue-router"],
  resolvers: [ElementPlusResolver()],
  // 新增如下
  dts: "src/auto-import.d.ts",
  eslintrc: {
    enabled: true
  },
}),
复制代码
```

eslintrc 中 enabled 设置为 true，保存之后会随即在跟目录下生成 .eslintrc-auto-import.json 文件。

```json
{
  "globals": {
    "EffectScope": true,
    "computed": true,
    "createApp": true,
    "customRef": true,
    "defineAsyncComponent": true,
    "defineComponent": true,
    "effectScope": true,
    "getCurrentInstance": true,
    "getCurrentScope": true,
    "h": true,
    "inject": true,
    "isProxy": true,
    "isReactive": true,
    "isReadonly": true,
    "isRef": true,
    "markRaw": true,
    "nextTick": true,
    "onActivated": true,
    "onBeforeMount": true,
    "onBeforeRouteLeave": true,
    "onBeforeRouteUpdate": true,
    "onBeforeUnmount": true,
    "onBeforeUpdate": true,
    "onDeactivated": true,
    "onErrorCaptured": true,
    "onMounted": true,
    "onRenderTracked": true,
    "onRenderTriggered": true,
    "onScopeDispose": true,
    "onServerPrefetch": true,
    "onUnmounted": true,
    "onUpdated": true,
    "provide": true,
    "reactive": true,
    "readonly": true,
    "ref": true,
    "resolveComponent": true,
    "resolveDirective": true,
    "shallowReactive": true,
    "shallowReadonly": true,
    "shallowRef": true,
    "toRaw": true,
    "toRef": true,
    "toRefs": true,
    "triggerRef": true,
    "unref": true,
    "useAttrs": true,
    "useCssModule": true,
    "useCssVars": true,
    "useLink": true,
    "useRoute": true,
    "useRouter": true,
    "useSlots": true,
    "watch": true,
    "watchEffect": true,
    "watchPostEffect": true,
    "watchSyncEffect": true
  }
}
复制代码
```

然后将这个文件引入 .eslintrc.cjs

```js
extends: [ 
    // ...
    './.eslintrc-auto-import.json' 
]
复制代码
```

到此，该问题就完美解决了。

### 问题2: 自动生成的 components.d.ts 文件内容有报错

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc0d8628be1a4931be4def2cf90e3fd9~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

解决办法：

修改 .d.ts 文件生成目录

```ts
Components({
  resolvers: [ElementPlusResolver()],
  // 新增如下
  dts: 'src/components.d.ts'
}),
复制代码
```

到此该问题也就 完美解决了。

[原文](https://juejin.cn/post/7169523949167411236)
