# Promise 知识点总结

> 基于 `frontend/src/store/modules/permission.js` 的 `generateRoutes` 方法，从零拆解 Promise 相关的 JS 语法与机制。
## 一、涉及的代码

```js
// store/modules/permission.js (L35-54)
generateRoutes(roles) {
  return new Promise(resolve => {
    getRouters().then(res => {
      const sdata = JSON.parse(JSON.stringify(res.data))
      const rdata = JSON.parse(JSON.stringify(res.data))
      const defaultData = JSON.parse(JSON.stringify(res.data))
      const sidebarRoutes = filterAsyncRouter(sdata)
      const rewriteRoutes = filterAsyncRouter(rdata, false, true)
      const defaultRoutes = filterAsyncRouter(defaultData)
      const asyncRoutes = filterDynamicRoutes(dynamicRoutes)
      asyncRoutes.forEach(route => { router.addRoute(route) })
      this.setRoutes(rewriteRoutes)
      this.setSidebarRouters(constantRoutes.concat(sidebarRoutes))
      this.setDefaultRoutes(sidebarRoutes)
      this.setTopbarRoutes(defaultRoutes)
      resolve(rewriteRoutes)
    })
  })
}
```

```js
// api/menu.js (L4-9)
export const getRouters = () => {
  return request({
    url: '/getRouters',
    method: 'get'
  })
}
```
## 二、语法层面拆解

`new Promise(resolve => { ... })` 同时叠加了以下 ES6 语法：
### 1. ES6 对象方法简写

```js
// Pinia actions 对象里的方法简写
generateRoutes(roles) { ... }
// 等价于
generateRoutes: function(roles) { ... }
```
### 2. 箭头函数（单参数省括号）

```js
resolve => { ... }          // 单参数，括号可省
(res) => { ... }            // 标准写法
function(resolve) { ... }   // 传统写法
```
### 3. Promise 构造函数

```js
new Promise(executor)
// executor 是一个函数，JS 引擎会自动调用它，并塞入两个实参：
// executor(resolve, reject)
//        ↑        ↑
//     兑现函数   拒绝函数（引擎造好传给你的）
```
## 三、Promise 是什么

### 核心概念

Promise 是一张"未来会有结果的承诺单"，让 JS 不用傻等异步任务就能继续干别的。

```

┌─────────┐  成功拿到结果   ┌───────────┐

│ pending │ ─────────────► │ fulfilled  │

│ (等待中) │                │  (已兑现)  │

└─────────┘                └───────────┘

       │

       │  失败

       ▼

┌───────────┐

│ rejected   │

│  (已拒绝)  │

└───────────┘

```
### 为什么需要 Promise

JS 是单线程的——同一时刻只能干一件事。如果发请求时傻等结果，整个网页会冻住。Promise 让异步任务"先发出去，等结果回来再通知你"，不阻塞主线程。

## 四、resolve 是什么

### 核心结论

**`resolve` 是 JS 引擎在 `new Promise(...)` 内部自动造的一个函数，作为实参传给 executor。你不需要实现它，只需要在合适的时机调用它。**

### 引擎内部伪代码

```js
class Promise {
  constructor(executor) {
    // ① 引擎造两个函数
    const resolve = (value) => {
      // 把 Promise 状态改成 fulfilled
      // 把 value 存起来给外面 .then() 用
    }
    const reject = (error) => {
      // 把 Promise 状态改成 rejected
    }
    // ② 调用 executor，把 resolve 和 reject 作为实参塞进去
    executor(resolve, reject)
  }
}
```

### 你只管"调用"，不管"实现"

```js
new Promise(resolve => {
  //       ↑ 形参，接收引擎传来的 resolve 函数
  resolve(rewriteRoutes)
  //  ↑ 你在"调用"它，不是在"实现"它！
  //    就像你调用 console.log() 一样
})
```

## 五、每个 Promise 搭配自己的 resolve

### 两个 Promise，两个 resolve，互不相干

```

Promise A（axios 造的）           Promise B（你 L36 造的）

─────────────────────           ─────────────────────

resolve A ← axios 持有           resolve B ← 你持有

状态：pending                     状态：pending

  │                               │

  │ 服务器响应回来                  │ 你在 L51 手动调

  ▼                               ▼

axios 调 resolve A(response)      你调 resolve B(rewriteRoutes)

  │                               │

  ▼                               ▼

fulfilled                        fulfilled

值 = response                     值 = rewriteRoutes

  │                               │

  ▼                               ▼

L38 的 .then(res => ...) 接到     调用端 await 接到

```
### 谁造的 Promise，谁就负责调 resolve

- **axios 内部**：`new Promise(resolve => { ... axios 自己调 resolve ... })`

- **你写的**：`new Promise(resolve => { ... 你自己在 L51 调 resolve ... })`

## 六、完整调用链路

### 三层 Promise

```

调用端（路由守卫）

  const accessRoutes = await store.generateRoutes()

                           │

                           ▼

Store 内部（L36-53）：手动包一层 Promise B

  return new Promise(resolve => {

    getRouters()                        ← 第 2 层 Promise

      .then(res => {

        // 处理数据...

        resolve(rewriteRoutes)           ← 兑现 Promise B

      })

  })

                           │

                           ▼

getRouters（menu.js）：原样 return request 结果

  return request({ url: '/getRouters', method: 'get' })

                           │

                           ▼

request（axios）：发 HTTP 请求，返回 Promise A

  等服务器响应回来 → 自动 resolve(response)

```
### 执行时序

```

L36:  new Promise(resolve => {     ← 造 Promise B，引擎塞 resolve B

L38:  getRouters()                ← 发请求，返回 Promise A（pending）

        .then(res => {             ← 注册：等 Promise A 兑现后执行

│

│  ───── 等网络请求飞一会儿 ─────

│

                                     服务器返回响应

                                     axios 内部调 resolve A(服务器数据)

                                     Promise A → fulfilled

│

L38-51 函数体开始执行               ← res 拿到服务器数据

  res.data 拿到路由 JSON

  深拷贝、filterAsyncRouter 处理

  setRoutes / setSidebarRouters 等

L51: resolve(rewriteRoutes)       ← 调 Promise B 的 resolve

                                     Promise B → fulfilled

│

调用端                              ← await 拿到 rewriteRoutes

```
## 七、闭包（Closure）

### 为什么 L51 能调用 L36 的 resolve

L51 在 `.then(res => {...})` 的内层函数里，而 `resolve` 是外层 executor 函数的形参。按后端（Java/C）的作用域规则，外层函数执行完，局部变量就该销毁——但 JS 有**闭包**。
### 闭包本质

内层函数"绑架"了外层作用域的变量，不让它销毁，随时可以用。
### 作用域嵌套图

```

┌─────────────────────────────────────────────────────┐

│  外层：executor 箭头函数（resolve => { ... }）       │

│  ├─ 形参：resolve（引擎塞进来的函数）                 │

│  ├─ 同步执行：调用 getRouters()，注册 .then 回调     │

│  └─ 外层函数执行完毕！按理 resolve 该销毁？           │

│                                                       │

│  ┌─────────────────────────────────────────────┐   │

│  │ 内层：.then 的箭头函数（res => { ... }）      │   │

│  │ ├─ 形参：res ← 网络回来后 axios 塞进来的响应    │   │

│  │ ├─ 闭包！抓着外层的 resolve 形参不放！         │   │

│  │ └─ L51：调用 resolve(rewriteRoutes) ← 合法！   │   │

│  └─────────────────────────────────────────────┘   │

└─────────────────────────────────────────────────────┘

```

---
## 八、不调 resolve 会怎样

| 层面 | 结果 |

|---|---|

| L36 `return` 返回的东西 | 一个 Promise 对象（与调不调 resolve 无关） |

| 这个 Promise 的状态 | **永远停留在 `pending`** |

| 调用端 `await store.generateRoutes()` | **永远卡住**，这一行之后的所有代码都不执行 |

### 关键区别

```js
// return 返回的是 Promise 对象本身，不是 resolve 传的值
const p = new Promise(resolve => { ... })
// p → Promise { <pending> } 或 Promise { <fulfilled>: 值 }
// await 是"等这张承诺单兑现，把兑现值取出来"
const 值 = await p
```
## 十、三种等价写法对比

```js
// 写法 1：Promise + .then 嵌套（当前代码）
const p1 = new Promise(resolve => {
  getRouters().then(res => {
    resolve(res.data)
  })
})
  
// 写法 2：直接 return Promise 链（推荐）
const p2 = getRouters().then(res => res.data)

// 写法 3：async/await（语法糖，最接近人类思维）
async function generateRoutes() {
  const res = await getRouters()
  return res.data
}
```
三者最终返回的都是 Promise，外面都可以用 `.then()` 或 `await` 接。

### 数据流追踪

```
store/modules/permission.js                          permission.js（调用端）
─────────────────────────────────────────────────────────────────────────

L36: return new Promise(resolve => {                 L48: const accessRoutes = await store.generateRoutes()
       ↑ 返回 Promise B（pending）                          ↑ await 等 Promise B 兑现，提取里面的值
                                                              │
L38:   getRouters().then(res => {                            │ 等
         │                                                   │
L42-43:   const rewriteRoutes = filterAsyncRouter(rdata,     │
           false, true)                                      │
         ↑ 这是个路由数组                                     │
         │                                                   │
L51:     resolve(rewriteRoutes)                             │
         ↑ 调 resolve B，把 rewriteRoutes 作为兑现值存入     │
           Promise B → fulfilled                            │
         │                                                   │
         └───────────────────────────────────────────────────┘
                                                Promise B 兑现，await 提取出 rewriteRoutes

                                             L49: accessRoutes.forEach(route => {
                                                      ↑ accessRoutes 就是 rewriteRoutes！
                                                      router.addRoute(route)
                                                    })
```

---

### 具体来说，`accessRoutes` 里装的是什么？

就是 `rewriteRoutes`，即 `filterAsyncRouter(rdata, false, true)` 的返回值——一个**路由对象数组**，结构类似：

```js
[
  {
    path: '/system',
    component: Layout,           // 已从字符串 'Layout' 转成组件对象
    children: [
      {
        path: 'user',
        component: () => import('views/system/user/index.vue'),  // 已转成懒加载函数
        // ...
      }
    ]
  },
  // ...
]
```

后端返回的是 JSON 字符串（`route.component: "Layout"`），`filterAsyncRouter` 把字符串转成真正的 Vue 组件对象，处理后返回这个数组。

---

### `await` 做的事

```js
// generateRoutes() 返回的是 Promise B 对象本身
const promiseB = store.generateRoutes()
// promiseB → Promise { <pending> }
//                ↑ 内部存着 rewriteRoutes（但还没兑现，拿不到）

// await 做了两件事：
// 1. 等 Promise B 状态变成 fulfilled（等 L51 的 resolve 被调用）
// 2. 提取出 resolve 传的值
const accessRoutes = await store.generateRoutes()
// accessRoutes → rewriteRoutes（那个路由数组）
```

---

### 为什么要 `await` 之后才 `forEach`

```js
// ❌ 不 await，拿到的是 Promise 对象，不是数组
const accessRoutes = store.generateRoutes()
accessRoutes.forEach(...) // 报错！Promise 没有 forEach 方法

// ✅ await 之后，拿到的是 resolve 传出的值（数组）
const accessRoutes = await store.generateRoutes()
accessRoutes.forEach(route => { router.addRoute(route) }) // 正确，遍历路由数组
```

---

### 一句话总结

> **L48 的 `await` 等 L51 的 `resolve(rewriteRoutes)` 执行后，提取出 `rewriteRoutes` 赋给 `accessRoutes`。L49 的 `accessRoutes` 就是 L51 `resolve` 传出去的那个 `rewriteRoutes` 数组。**



---


对，**外层 executor 先结束，内层 `.then` 回调后执行**——这就是异步通知的本质，闭包让这成为可能。

---

### 执行时序：外层先结束，内层后跑

```
同步阶段（立刻执行，毫秒级）
─────────────────────────────────────────────────────
L35:  generateRoutes() 被调用
L36:  new Promise(resolve => {     ← 引擎造 resolve B，调 executor
L38:    getRouters()              ← 发 HTTP 请求（不等！立刻往下走）
        .then(res => {            ← 注册回调（还不执行！只是登记）
        })
      })                          ← executor 执行完毕！
L53:  })                          ← Promise B 返回（状态 pending）
L36:  return Promise B             ← generateRoutes 返回，外层结束！

调用端: await store.generateRoutes()
       ↑ 拿到 Promise B（pending），开始等

  ═══════ 外层已结束，但 resolve B 活着 ═══════
  ═══════ 因为内层回调通过闭包抓着它不放 ═══════

异步阶段（等网络响应回来，可能几百毫秒后）
─────────────────────────────────────────────────────
      服务器响应回来
      axios 调 resolve A(response)
      Promise A → fulfilled

L38:    .then(res => {            ← 现在内层回调才开始执行！
          res = response
          // L39-50: 处理数据...
L51:      resolve(rewriteRoutes)   ← 闭包抓到 resolve B，调它！
          Promise B → fulfilled
        })

调用端: await 拿到 rewriteRoutes，继续执行
```

---

### 为什么外层先结束、内层后执行还不报错

```
外层 executor 函数（L36-53）
  ├─ 同步执行：发请求 + 注册回调
  └─ 执行完毕 ← 按理 resolve B 形参该销毁？

      │  闭包！内层回调抓着 resolve B 不放
      │  resolve B 不会销毁，一直待命
      ▼

异步事件（网络响应回来）
  │
  ▼
内层 .then 回调执行
  ├─ res = axios 传进来的响应数据
  ├─ 处理数据
  └─ resolve(rewriteRoutes)  ← 通过闭包拿到 resolve B，合法调用！
```

---

### 关键点：异步通知 = 注册 + 后续触发

整个过程分两步：

| 步骤 | 代码 | 什么时候执行 | 干了什么 |
|---|---|---|---|
| **注册回调** | L38 `.then(res => {...})` | **同步阶段，立刻执行** | 告诉 Promise A："等你兑现了，就调这个函数" |
| **触发回调** | L39-51 `.then` 的函数体 | **异步阶段，网络回来后** | 回调实际执行，处理数据，调 resolve B |

**如果没有闭包**：外层 executor 结束后 `resolve B` 销毁，内层回调就没法调它了，Promise B 永远 pending，`await` 永远卡死。

**有了闭包**：内层回调抓着 `resolve B` 不放，等异步事件触发时照样能调用。

---

### 一句话总结

> **外层 executor 同步执行完就结束了（发请求 + 注册回调），但内层 `.then` 回调要等网络响应回来才异步执行。闭包让内层回调抓着外层的 `resolve` 不放，所以外层结束后内层照样能调到 `resolve`。这就是异步通知的本质——先注册，后触发。**