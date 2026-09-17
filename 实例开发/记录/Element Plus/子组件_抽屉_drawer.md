```
path: "/system/dict-data"
```

与 `children` 共同构成父子路由；`hidden: true` 只负责“不显示在左侧菜单”，不是形成父路由的原因。

## 1. 父子路由如何组成地址

项目路由是：

```
{
  path: "/system/dict-data",
  component: Layout,
  hidden: true,
  children: [
    {
      path: "index/:dictId(\\d+)",
      component: () => import("@/views/system/dict/data")
    }
  ]
}
```

父路径：

```
/system/dict-data
```

子路径：

```
index/:dictId
```

合并后：

```
/system/dict-data/index/:dictId
```

当 `dictId = 1` 时就是：

```
/system/dict-data/index/1
```

因此：

```
父路由 /system/dict-data
├── component: Layout
└── 子路由 index/1
    └── component: dict/data.vue
```

### `Layout` 和 `data.vue` 分别显示什么

父路由的：

```
component: Layout
```

提供若依整体框架：

- 左侧菜单；
- 顶部导航；
- 页签栏；
- 中间内容区域。

子路由的：

```
component: () => import("@/views/system/dict/data")
```

把 [data.vue](E:/WorkSpace/ruoyi-archive-management/frontend/src/views/system/dict/data.vue) 放进中间内容区域。

---

## 2. `hidden: true` 只负责隐藏菜单

```
hidden: true
```

含义是：

> 不把 `/system/dict-data` 这一组路由显示成左侧菜单项。

它不会：

- 禁止访问；
- 隐藏页面内容；
- 自动创建父子关系；
- 决定加载哪个 `.vue`。

父子关系来自：

```
{
  path: "父路径",
  children: [
    {
      path: "子路径"
    }
  ]
}
```

如果删除 `hidden: true`，父子路由依然存在，只是它有可能出现在菜单区域。

---

# `dict/detail.vue` 是什么

你没在路由配置中看到它，是因为它根本不是路由页面。

[detail.vue](E:/WorkSpace/ruoyi-archive-management/frontend/src/views/system/dict/detail.vue) 是一个“抽屉组件”，被嵌入字典管理的 `index.vue` 中使用。

当前项目实际上为字典数据提供了两种查看方式：

```
方式一：点击字典类型文字
        ↓
打开 detail.vue 抽屉
        ↓
地址不变化

方式二：点击“列表”按钮
        ↓
路由跳转到 data.vue
        ↓
地址变成 /system/dict-data/index/1
```

## 3. `detail.vue` 是如何被引入的

在字典管理页面中：

[index.vue (line 184)](E:/WorkSpace/ruoyi-archive-management/frontend/src/views/system/dict/index.vue:184)

```
import DictDataDrawer from "./detail"
```

这里的：

```
"./detail"
```

实际就是：

```
./detail.vue
```

导入后给它取了组件名称：

```
DictDataDrawer
```

所以在 `<template>` 中写成：

[index.vue (line 179)](E:/WorkSpace/ruoyi-archive-management/frontend/src/views/system/dict/index.vue:179)

```
<dict-data-drawer
  v-model:visible="drawerVisible"
  :row="drawerRow"
/>
```

也就是说：

```
文件名：detail.vue
导入名称：DictDataDrawer
模板标签：<dict-data-drawer>
```

这是同一个组件的三种表示方式。

---

## 4. 什么操作会打开 `detail.vue`

表格中的“字典类型”文字绑定了点击事件：

[index.vue (line 110)](E:/WorkSpace/ruoyi-archive-management/frontend/src/views/system/dict/index.vue:110)

```
<a
  class="link-type"
  @click="handleViewData(scope.row)"
>
  {{ scope.row.dictType }}
</a>
```

例如点击：

```
sys_user_sex
```

会执行：

```
handleViewData(scope.row)
```

对应方法：

[index.vue (line 280)](E:/WorkSpace/ruoyi-archive-management/frontend/src/views/system/dict/index.vue:280)

```
function handleViewData(row) {
  drawerRow.value = row
  drawerVisible.value = true
}
```

分两步：

```
drawerRow.value = row
```

将当前字典类型交给抽屉组件。

```
drawerVisible.value = true
```

让抽屉显示出来。

由于没有调用：

```
router.push(...)
```

所以浏览器地址仍然是：

```
/system/dict
```

---

## 5. `detail.vue` 如何接收数据

`index.vue` 传入：

```
:row="drawerRow"
```

`detail.vue` 通过 `defineProps()` 接收：

```
const props = defineProps({
  visible: {
    type: Boolean,
    default: false
  },
  row: {
    type: Object,
    default: () => ({})
  }
})
```

其中：

```
props.visible   是否显示抽屉
props.row       当前点击的字典类型
```

`row` 可能类似：

```
{
  dictId: 1,
  dictName: "用户性别",
  dictType: "sys_user_sex"
}
```

---

## 6. 抽屉打开后查询数据

`detail.vue` 监听 `visible`：

```
watch(
  () => props.visible,
  (val) => {
    if (val) {
      loadData()
    } else {
      dataList.value = []
    }
  }
)
```

抽屉打开时：

```
val === true
```

于是调用：

```
loadData()
```

查询方法是：

```
function loadData() {
  if (!props.row?.dictType) return

  loading.value = true
  dataList.value = []

  listData({
    dictType: props.row.dictType,
    pageSize: 100,
    pageNum: 1
  }).then(response => {
    dataList.value = response.rows || []
  }).catch(() => {
  }).finally(() => {
    loading.value = false
  })
}
```

它直接使用当前行已经存在的：

```
props.row.dictType
```

查询字典数据。

例如：

```
dictType: "sys_user_sex"
```

最终查询：

```
GET /system/dict/data/list
    ?dictType=sys_user_sex
    &pageSize=100
    &pageNum=1
```

---

## 7. `detail.vue` 和 `data.vue` 的区别

|对比项|`detail.vue`|`data.vue`|
|---|---|---|
|类型|普通子组件|路由页面|
|显示形式|右侧抽屉|完整内容页面|
|浏览器地址|不变化|变为 `/system/dict-data/index/1`|
|打开方式|修改 `drawerVisible`|`$tab.openPage()`|
|是否配置路由|不需要|必须配置|
|功能|快速预览字典数据|完整管理字典数据|
|是否可以增删改|当前没有|有完整管理功能|

因此可以这样记：

```
detail.vue = 快速看一下
data.vue   = 进入完整管理页面
```

## 8. 两条真实流程

### 点击蓝色字典类型

```
点击 sys_user_sex
        ↓
handleViewData(row)
        ↓
drawerRow = row
drawerVisible = true
        ↓
显示 detail.vue
        ↓
根据 row.dictType 查询数据
        ↓
URL仍是 /system/dict
```

### 点击“列表”

```
点击“列表”
        ↓
handleDataList(row)
        ↓
$tab.openPage(...)
        ↓
跳转 /system/dict-data/index/1
        ↓
路由加载 data.vue
        ↓
data.vue读取 route.params.dictId
        ↓
显示完整字典数据管理页面
```

所以你没在路由里看到 `detail.vue` 是正常的。只有“页面级组件”需要路由；嵌入页面内部的抽屉、弹窗、表单组件不需要单独配置路由。

顺便指出当前 `detail.vue` 中有一处明显的临时文字：

```
<div class="stat-label">共计条目000000</div>
```

这里的 `000000` 看起来是开发过程中留下的测试内容，正常应当只是：

```
<div class="stat-label">共计条目</div>
```

这和路由无关，但确实是当前项目代码中容易忽略的细节。