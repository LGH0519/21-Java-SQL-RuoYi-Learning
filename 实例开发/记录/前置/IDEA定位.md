在若依后端中，不建议靠手动展开文件夹寻找。最快的方法是从“类名、接口地址、前端请求地址”反向定位。

## 一、已知类名：直接搜索类

例如已知 `SysLoginController`。

在 IntelliJ IDEA 中：

- 按两次 `Shift`
- 输入 `SysLoginController`
- 回车打开

或者使用：

```
Ctrl + N
```

输入 Java 类名。

这是最快的方式，不需要记住文件夹。

`SysLoginController.java` 的实际位置是：

```
backend
└─ ruoyi-admin
   └─ src/main/java
      └─ com/ruoyi/web/controller/system
         └─ SysLoginController.java
```

它第一行的包名：

```
package com.ruoyi.web.controller.system;
```

与目录基本一一对应：

```
com.ruoyi.web.controller.system
              ↓
com/ruoyi/web/controller/system
```

## 二、只知道中文功能：全局搜索

例如只知道“登录”。

按：

```
Ctrl + Shift + F
```

搜索：

```
登录
```

但中文“登录”可能出现很多次，更推荐搜索有代表性的代码：

```
@PostMapping("/login")
```

或者：

```
/login
```

搜索结果里通常能找到：

```
@PostMapping("/login")
public AjaxResult login(...)
```

### 四种搜索快捷键的区别

| 快捷键                | 用途        | 示例                          |
| ------------------ | --------- | --------------------------- |
| `Shift` 两次         | 搜索所有内容    | `SysLoginController`        |
| `Ctrl + N`         | 搜索 Java 类 | `BizRegistrationController` |
| `Ctrl + Shift + N` | 搜索文件      | `application.yml`           |
| `Ctrl + Shift + F` | 搜索文件内容    | `/registration/personal`    |

## 三、已知接口地址：搜索 `@RequestMapping`

假设浏览器开发者工具中看到请求：

```
GET /registration/personal/list
```

后端一般拆成两部分：

Controller 类上：

```
@RequestMapping("/registration/personal")
```

方法上：

```
@GetMapping("/list")
```

拼接后就是：

```
/registration/personal + /list
= /registration/personal/list
```

因此可以全局搜索：

```
@RequestMapping("/registration/personal")
```

就能定位到：

```
BizRegistrationController.java
```

这是排查接口时最实用的方法。

## 四、从前端反向寻找后端

若依的前端接口通常放在：

```
frontend/src/api
```

例如前端代码：

```
return request({
  url: '/registration/personal/list',
  method: 'get'
})
```

定位过程是：

```
前端 .vue
  ↓ 看 import
frontend/src/api/registration/personal.js
  ↓ 看 url
/registration/personal/list
  ↓ 全局搜索后端 @RequestMapping
BizRegistrationController.java
```

例如 Vue 中出现：

```
import { listPersonal } from "@/api/registration/personal"
```

说明它来自：

```
frontend/src/api/registration/personal.js
```

然后在这个文件里找到请求地址，再去后端搜索。

## 五、找到 Controller 后继续追踪

Controller 一般不是直接操作数据库，而是按下面的调用顺序：

```
Controller
    ↓
Service 接口
    ↓
ServiceImpl 实现类
    ↓
Mapper.java
    ↓
Mapper.xml
    ↓
数据库表
```

以报名查询为例：

```
bizRegistrationService.selectBizRegistrationList(...)
```

在 IDEA 中按住 `Ctrl`，点击：

```
selectBizRegistrationList
```

就能跳转到它的定义。

常用快捷键：

- `Ctrl + 鼠标左键`：跳转到定义。
- `Ctrl + B`：跳转到定义。
- `Ctrl + Alt + B`：跳转到实现类。
- `Alt + 左方向键`：返回上一个位置。

典型追踪过程：

```
BizRegistrationController.java
    ↓ Ctrl + 鼠标点击
IBizRegistrationService.java
    ↓ Ctrl + Alt + B
BizRegistrationServiceImpl.java
    ↓ Ctrl + 鼠标点击
BizRegistrationMapper.java
    ↓ 搜索同名方法
BizRegistrationMapper.xml
```

## 六、若依后端各模块大致负责什么

你的项目中常见模块可以先这样理解：

```
backend
├─ ruoyi-admin
├─ ruoyi-framework
├─ ruoyi-system
├─ ruoyi-common
├─ ruoyi-generator
└─ ruoyi-quartz
```

### `ruoyi-admin`

应用启动入口，以及部分系统入口 Controller。

例如：

```
SysLoginController
SysIndexController
```

`SysLoginController` 放在这里，是因为登录属于整个系统的入口功能。

### `ruoyi-system`

用户、角色、菜单、字典以及你新增的普通业务代码。

你的报名功能主要在这里：

```
domain
mapper
service
service/impl
controller
resources/mapper
```

### `ruoyi-framework`

安全认证、权限检查、Token、异常处理等框架功能。

例如想了解：

```
用户登录成功后怎样生成 Token
```

一般会继续追踪到这个模块。

### `ruoyi-common`

公共工具和通用基础类，例如：

```
AjaxResult
BaseController
StringUtils
```

### `ruoyi-generator`

代码生成器相关逻辑。

### `ruoyi-quartz`

定时任务相关功能。

## 七、一个容易产生的误解

不能简单认为：

```
登录功能 = SysLoginController 中的全部代码
```

`SysLoginController` 只是登录请求的入口，真正的登录过程可能继续调用：

```
SysLoginController
    ↓
SysLoginService
    ↓
UserDetailsServiceImpl
    ↓
AuthenticationManager
    ↓
TokenService
```

因此，找到 Controller 只是找到入口，并不代表找到了完整实现。

## 最推荐的定位方法

以后遇到一个功能，按这个顺序寻找：

```
1. 在浏览器按 F12
2. 找到 Network 中的接口地址
3. 在前端项目搜索接口地址
4. 在后端搜索 @RequestMapping
5. 找到 Controller
6. Ctrl+点击继续追踪 Service、Mapper
```

以你现在的报名功能为例：

```
页面调用 /registration/personal/list
                   ↓
搜索 @RequestMapping("/registration/personal")
                   ↓
找到 BizRegistrationController
                   ↓
找到 selectBizRegistrationList
                   ↓
继续进入 Service 和 Mapper.xml
```

这个方法比记忆每个文件所在文件夹更重要。目录可能变化，但接口地址和方法调用链总能帮助你定位代码。