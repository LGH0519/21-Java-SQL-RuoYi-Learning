---
title: Vite/Webpack 中 Loader 与 Plugin 的区别
tags:
  - 前端
  - Vite
  - Webpack
  - 若依
  - 工程化
created: \{{date:YYYY-MM-DD}}
---
# Vite/Webpack 中 Loader 与 Plugin 的区别

> [!NOTE] 一句话总结
> **Loader 负责“翻译文件”**（怎么处理内容），**Plugin 负责“干杂活”**（在流程中做什么事）。

## 核心对比

| 概念 | 角色 | 关注点 | 若依/Vite 示例 |
| :--- | :--- | :--- | :--- |
| **Loader** | 翻译官 | 文件内容怎么变 | `vue-loader` 把 `.vue` 转成 JS |
| **Plugin** | 项目经理 | 构建流程中做什么 | `HtmlPlugin` 自动注入 script |

## 执行顺序

1. **Loader 先处理文件**（`.vue` → JS）
2. **Plugin 再介入流程**（压缩、生成 HTML）

## 若依中的常见应用

- **Loader**：`vue-loader`、`css-loader`、`sass-loader`
- **Plugin**：`vue()`、`HtmlPlugin`、`Components()` 自动导入

> [!TIP] 记忆口诀
> **Loader 管“文件”，Plugin 管“流程”**。

|概念|角色|关注点|若依/Vite 中的例子|
|---|---|---|---|
|**Loader**​|**翻译官**​|**“这个文件怎么变成 JS？”**​|把 `.vue` 文件、`.scss` 文件变成浏览器能懂的 JS/CSS|
|**Plugin**​|**项目经理**​|**“打包过程中要额外做什么？”**​|把打包结果压缩、把 `index.html` 注入 script、把静态资源复制到 `dist`|
## 🏭 一、用“工厂流水线”来类比

把 **Vite/Webpack**​ 想象成一条**汽车组装流水线**：

- **原材料**：`.vue`、`.scss`、`.png`、`.ts` 等源文件
- **流水线**：Vite/Webpack 的构建过程
- **成品**：`dist` 目录下的 `.js`、`.css`、`.html`

### 1️⃣ Loader = 翻译官（处理“文件内容”）

> **问题**：浏览器只认识 JS、CSS、HTML，不认识 `.vue`、`.scss`、`.ts`。

**Loader 的作用**：

在文件进入流水线之前，**把非 JS 文件翻译成 JS 或 CSS**。

|若依中的文件|使用的 Loader|翻译结果|
|---|---|---|
|`.vue`|`vue-loader`|拆成 `<template>` → render 函数，`<style>` → CSS|
|`.scss` / `.less`|`sass-loader` / `less-loader`|编译成普通 CSS|
|`.ts`|`ts-loader` / `esbuild`|转成 JS|
|`.png` / `.svg`|`url-loader` / `file-loader`|变成路径或 Base64|

**一句话**：

> **Loader 只关心“文件内容怎么变”**。

---

### 2️⃣ Plugin = 项目经理（处理“打包流程”）

> **问题**：翻译完文件后，还需要很多“额外工作”。

**Plugin 的作用**：

在打包流程的**特定时机**（开始、结束、生成文件前）插入自定义逻辑。

|若依/Vite 场景|Plugin 名称|干了什么|
|---|---|---|
|打包 HTML|`HtmlWebpackPlugin` / Vite 内置|自动把打包后的 JS/CSS 注入到 `index.html`|
|清空目录|`CleanWebpackPlugin`|每次打包前清空 `dist`|
|压缩代码|`TerserPlugin`|把 JS 压缩成一行，体积更小|
|复制静态资源|`CopyWebpackPlugin`|把 `public/` 目录原样复制到 `dist`|
|热更新|Vite 内置 HMR Plugin|改完代码不刷新页面，只更新变化的模块|

**一句话**：

> **Plugin 不关心文件内容，只关心“打包流程中做什么事”**。

---

## 🔄 二、Loader 和 Plugin 的执行顺序（Vite）

在 **Vite（若依 Vue3 使用）**​ 中，流程大致是：

```mermaid
flowchart LR
    A[源代码 .vue/.ts/.scss] --> B[Loader 处理]
    B --> C[模块转换完成]
    C --> D[Plugin 介入]
    D --> E[生成 bundle]
    E --> F[输出 dist]
```

1. **Loader 先干活**：把 `.vue`、`.scss` 转成 JS
2. **Plugin 后介入**：压缩、注入 HTML、复制文件

---

## 🧠 三、若依项目中你“已经用过”的 Loader / Plugin

### 你一定见过的 Loader

在 `ruoyi-ui` 里：

- 写 `.vue` 文件 → 用到了 `vue-loader`
- 写 `style="color: red"` → 用到了 `css-loader`
- 写 `import api from '@/api'` → 用到了 `alias` + `resolve`（本质是 Plugin）

### 你一定见过的 Plugin

在 `vite.config.js` 里：

```js
export default defineConfig({
  plugins: [
    vue(),          // Vue 官方 Plugin
    Components({    // 自动导入组件
      resolvers: [ElementPlusResolver()]
    })
  ]
})
```

---

## ✅ 四、终极对比表（面试/复习用）

| 维度        | Loader       | Plugin                                 |
| --------- | ------------ | -------------------------------------- |
| **本质**​   | 文件转换器        | 构建钩子函数                                 |
| **关注点**​  | 文件内容         | 构建流程                                   |
| **执行时机**​ | 文件加载阶段       | 整个构建生命周期                               |
| **典型场景**​ | `.vue` → JS  | 压缩、注入 HTML                             |
| **若依例子**​ | `vue-loader` | `HtmlPlugin`、`vite-plugin-compression` |
