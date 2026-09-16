点击“个人报名”涉及两段流程：

1. 登录/刷新时：从后端取得菜单并生成路由。
2. 真正点击菜单时：前端跳转路由并加载Vue页面。

最容易误解的一点是：

> 每次点击“个人报名”通常不会重新查询`sys_menu`。菜单路由一般在登录或刷新后的首次鉴权时就已经加载好了。

## 完整流程图

```
登录或刷新
   ↓
前端请求 GET /getRouters
   ↓
后端查询当前用户拥有的 sys_menu
   ↓
返回 component = registration/personal/index
   ↓
前端将字符串转换成Vue组件加载函数
   ↓
注册路由 /registration
   ↓
左侧显示“个人报名”
   ↓
用户点击“个人报名”
   ↓
SidebarItem计算跳转地址
   ↓
router-link执行路由跳转
   ↓
全局路由守卫检查Token和权限
   ↓
加载 registration/personal/index.vue
   ↓
执行该Vue文件的<script setup>
   ↓
useDict加载状态、职位字典
   ↓
渲染<template>
```

你可以分两遍Debug，否则第一次跟踪容易乱。

# 第一遍：Debug菜单怎样从数据库来到前端

这部分要退出登录再重新登录，或者刷新后清空前端状态，因为`getRouters`不是每次点击菜单都调用。

## 第一个后端断点

打开：

[SysLoginController.java (line 102)](E:/WorkSpace/ruoyi-archive-management/backend/ruoyi-admin/src/main/java/com/ruoyi/web/controller/system/SysLoginController.java:102)

在第102行打断点：

```
public AjaxResult getRouters()
```

登录后，前端请求：

```
GET /getRouters
```

程序会停在这里。

### 第104行

```
Long userId = SecurityUtils.getUserId();
```

作用：

> 获得当前登录用户的用户ID。

你可以观察`userId`，确认是不是测试报名人员的ID。

### 第105行

```
List<SysMenu> menus =
    menuService.selectMenuTreeByUserId(userId);
```

作用：

> 根据用户ID查询这个用户拥有的菜单。

执行完这一行后，观察`menus`，里面应该包含“个人报名”。

### 第106行

```
return AjaxResult.success(
    menuService.buildMenus(menus)
);
```

作用：

> 把数据库菜单转换成前端能够使用的路由格式。

你截图中的：

```
{
  "component": "registration/personal/index",
  "path": "registration"
}
```

就是在这里返回的。

## 第二个前端断点

打开：

[permission.js (line 38)](E:/WorkSpace/ruoyi-archive-management/frontend/src/store/modules/permission.js:38)

在第38行打断点：

```
getRouters().then(res => {
```

后端返回以后，程序会停在这里。

查看：

```
res.data
```

你应该能找到：

```
component: "registration/personal/index"
```

## 第三个前端断点

同一文件第59行：

```
function filterAsyncRouter(
  asyncRouterMap,
  lastRouter = false,
  type = false
) {
```

这个函数负责遍历后端返回的所有路由。

执行到第73行：

```
route.component = loadView(route.component)
```

此时：

```
route.component
```

原来只是字符串：

```
registration/personal/index
```

执行以后，它会变成一个可以加载Vue文件的函数。

## 第四个前端断点

同一文件第116行：

```
export const loadView = (view) => {
```

当`view`传进来时，观察它：

```
view
```

应该是：

```
registration/personal/index
```

第119行：

```
const dir =
  path.split('views/')[1].split('.vue')[0]
```

会从全部Vue文件中得到文件路径。

例如：

```
./../../views/registration/personal/index.vue
```

处理后得到：

```
registration/personal/index
```

第120行比较：

```
if (dir === view)
```

实际比较的是：

```
registration/personal/index
===
registration/personal/index
```

相同就进入第121行：

```
res = () => modules[path]()
```

这一步不是立刻显示页面，而是准备一个“需要时加载该Vue文件”的函数。

# 第二遍：Debug真正点击“个人报名”

路由已经生成以后，再跟踪点击行为。

## 第一个断点：计算点击地址

打开：

[SidebarItem.vue (line 79)](E:/WorkSpace/ruoyi-archive-management/frontend/src/layout/components/Sidebar/SidebarItem.vue:79)

在第79行打断点：

```
function resolvePath(routePath, routeQuery) {
```

点击“个人报名”时会进入这个函数。

可以观察：

```
routePath
props.basePath
```

最后第90行：

```
return getNormalPath(
  props.basePath + '/' + routePath
)
```

计算出最终跳转地址，例如：

```
/registration
```

## 第二个断点：生成`router-link`

打开：

[Link.vue (line 28)](E:/WorkSpace/ruoyi-archive-management/frontend/src/layout/components/Sidebar/Link.vue:28)

在第28行打断点：

```
function linkProps() {
```

普通内部菜单最后返回：

```
return {
  to: props.to
}
```

这里的`to`就是即将跳转的地址，例如：

```
/registration
```

这个结果交给Vue Router的：

```
<router-link>
```

## 第三个断点：进入路由守卫

打开：

[permission.js (line 21)](E:/WorkSpace/ruoyi-archive-management/frontend/src/permission.js:21)

在第21行打断点：

```
router.beforeEach(async (to, from) => {
```

点击菜单后一定会经过这里。

观察：

```
from.path
to.path
```

可能是：

```
from.path = "/index"
to.path   = "/registration"
```

这里会检查：

- 有没有登录Token；
- 是否处于锁屏状态；
- 用户信息是否已经加载；
- 动态路由是否已经生成。

如果已经登录且路由已经生成，会执行到：

```
return true
```

表示允许进入个人报名页面。

## 第四个断点：进入你的Vue页面

你的页面是：

[index.vue](E:/WorkSpace/ruoyi-archive-management/frontend/src/views/registration/personal/index.vue)

由于当前`<script setup>`开头没有函数，第一次练习可以临时加一行：

```
<script setup name="Personal">
debugger

import { useDict } from "@/utils/dict"
```

然后：

1. 打开浏览器开发者工具；
2. 点击“个人报名”；
3. 浏览器会停在`debugger`；
4. 这说明路由已经成功找到并开始执行该Vue文件。

验证完成后删除：

```
debugger
```

## 第五个断点：加载字典

打开：

[dict.js (line 7)](E:/WorkSpace/ruoyi-archive-management/frontend/src/utils/dict.js:7)

在第7行打断点：

```
export function useDict(...args) {
```

观察：

```
args
```

它应该包含：

```
[
  "registration_position",
  "registration_status"
]
```

然后第12行检查前端缓存：

```
const dicts = useDictStore().getDict(dictType)
```

两种情况：

```
缓存中有字典
→ 直接使用，不请求后端

缓存中没有字典
→ 执行getDicts(dictType)
→ 请求后端
```

# 第一次建议关注的断点

不用一次打十几个。第一次只打下面5个：

| 顺序  | 文件                            | 行   | 观察内容               |
| --- | ----------------------------- | --- | ------------------ |
| 1   | `SysLoginController.java`     | 102 | 后端开始处理`getRouters` |
| 2   | `store/modules/permission.js` | 38  | 前端收到路由数据           |
| 3   | `store/modules/permission.js` | 116 | 组件路径怎样匹配Vue文件      |
| 4   | `SidebarItem.vue`             | 79  | 点击后生成什么地址          |
| 5   | `src/permission.js`           | 21  | 路由跳转到哪里            |

最后在自己的`index.vue`中临时加：

```
debugger
```

这样你能看见完整路线：

```
数据库菜单
→ 后端Controller
→ 前端动态路由
→ 左侧菜单点击
→ 路由守卫
→ 个人报名index.vue
```

注意：后端Java断点需要用IDE的Debug模式启动后端；只用普通Run启动，Java断点不会停。前端断点可以直接在浏览器开发者工具的Sources中设置。