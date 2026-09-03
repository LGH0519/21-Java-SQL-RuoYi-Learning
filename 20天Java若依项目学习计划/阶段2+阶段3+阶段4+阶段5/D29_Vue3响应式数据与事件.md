# D29｜Vue 3：响应式数据、模板与事件

> 今日目标：读懂若依字典页面的 template/script，能解释 `ref`、`v-model`、`@click`、`v-for`，在独立练习中完成表单→数组→表格。

## 1. 知识速记

- Vue 组件通常由 `<template>`（页面结构）和 `<script setup>`（数据/行为）组成。
- `ref` 保存响应式基本值；修改 `.value` 后视图更新。
- `v-model` 是输入控件与变量的双向绑定；`@click` 绑定点击事件；`v-for` 循环渲染列表。
- Vue 的响应式数据仍在浏览器内，调用 API 后才会和后端同步。

## 2. 资料

- [Vue 官方：响应式基础](https://cn.vuejs.org/guide/essentials/reactivity-fundamentals.html)
- [Vue 官方：列表渲染](https://cn.vuejs.org/guide/essentials/list.html)
- `E:\WorkSpace\ruoyi-archive-management\frontend\src\views\system\dict\index.vue`

## 3. 知识详解

在若依页面中，`queryParams` 是查询条件，`getList` 是点击查询后执行的函数，`dictTypeList` 等列表变量驱动表格。`v-model` 不等于后端参数绑定，它只是 Vue 中表单值变化同步到 JS 变量；网络请求由 API 方法显式发出。

## 4. 今天照做

### 09:00–12:00｜只读真实 Vue 文件

1. IDEA 打开 `frontend\src\views\system\dict\index.vue`。
2. 用折叠/搜索分别找到 `<template>`、`<script setup>`（或项目实际脚本形式）、`ref`/`reactive`、`v-model`、`@click`、`v-for`。
3. 建表记录：语法、绑定变量/函数、页面效果。不要改文件。

### 15:00–18:00｜独立 Vue 练习

1. 在自己已有的 Node 学习目录，用 Vite Vue 模板建立空练习；若不熟命令，先只在浏览器打开 [Vue Playground](https://play.vuejs.org/) ，不影响若依项目。
2. 在 Playground 的 `App.vue` 输入：一个 `ref('')` 档案名称、一个 `ref([])` 数组、输入框 `v-model`、按钮 `@click` 将对象 push 进数组、表格 `v-for` 展示。
3. 每个循环行提供 `:key="item.id"`；id 可用递增数字。

### 19:00–23:00｜验证响应式

1. 输入三条不同档案，点击新增；确认表格即时刷新。
2. Console 打印数组，解释点击函数改变的是什么。
3. 回到若依字典页，用同样视角解释一次“查询表单→getList→表格变量”。

## 5. AI Agent 操作卡

贴不超过一个 Vue 组件，要求 AI 标出“状态、事件、模板绑定、未接 API 的边界”，并给 3 个手工测试步骤；禁止它修改若依组件或自动加请求。

## 6. 验收与文末答案

- [ ] 能在字典页定位六种 Vue 语法。
- [ ] 独立页面完成表单输入→数组新增→表格显示。
- [ ] 能解释为何此次新增不会写入 MySQL。

答案：`ref` 作用？保存响应式状态；JS 中通常通过 `.value` 改它。`v-model` 是否自动调接口？否。
