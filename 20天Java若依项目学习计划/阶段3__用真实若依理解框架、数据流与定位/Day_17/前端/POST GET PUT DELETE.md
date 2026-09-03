好问题！让我从**HTTP 协议规范**和**若依项目实践**两个角度来解答。

---

## 📋 一、POST 和 GET 的使用场景

### 🎯 核心区别（一句话总结）

| 方法 | 用途 | 数据位置 | 安全性 | 幂等性 |
|-----|------|---------|--------|--------|
| **GET** | **查询**（读取数据） | URL 参数 | ❌ 不安全（URL 可见） | ✅ 是（多次执行结果一样） |
| **POST** | **提交/修改**（创建/更新/删除） | Request Body | ✅ 较安全（Body 不可见） | ❌ 否（可能产生副作用） |

---

### 📌 若依项目中的使用规范

#### ✅ GET — 用于查询操作

```java
// 1. 查询列表
@GetMapping("/list")
public TableDataInfo list(SysUser user)
{
    // 查询用户列表
}

// 2. 查询详情
@GetMapping("/{userId}")
public AjaxResult getInfo(@PathVariable("userId") Long userId)
{
    // 查询单个用户
}

// 3. 导出
@GetMapping("/export")
public void export(HttpServletResponse response, SysUser user)
{
    // 导出 Excel
}

// 4. 登录后的信息获取
@GetMapping("getInfo")
public AjaxResult getInfo()
{
    // 获取用户信息（只读）
}

@GetMapping("getRouters")
public AjaxResult getRouters()
{
    // 获取路由菜单（只读）
}
```

**特点**：
- 不修改数据
- 参数放在 URL 中（`?userId=1&name=admin`）
- 可以收藏、分享、刷新

---

#### ✅ POST — 用于创建/登录操作

```java
// 1. 登录（提交凭证）
@PostMapping("/login")
public AjaxResult login(@RequestBody LoginBody loginBody)
{
    // 验证用户名密码，返回 token
}

// 2. 新增用户
@PostMapping
public AjaxResult add(@Validated @RequestBody SysUser user)
{
    // 插入新记录
    return toAjax(userService.insertUser(user));
}

// 3. 上传文件
@PostMapping("/upload")
public AjaxResult upload(MultipartFile file)
{
    // 上传文件到服务器
}
```

**特点**：
- 会修改数据（创建新记录）
- 数据放在 Request Body 中（JSON）
- 不能收藏、分享

---

#### ✅ PUT — 用于更新操作

```java
// 修改用户
@PutMapping
public AjaxResult edit(@Validated @RequestBody SysUser user)
{
    // 更新已有记录
    return toAjax(userService.updateUser(user));
}
```

**特点**：
- 修改已有数据
- 数据放在 Request Body 中
- 全量更新（替换整个资源）

---

#### ✅ DELETE — 用于删除操作

```java
// 删除用户
@DeleteMapping("/{userIds}")
public AjaxResult remove(@PathVariable Long[] userIds)
{
    // 删除记录
    return toAjax(userService.deleteUserByIds(userIds));
}
```

**特点**：
- 删除数据
- 参数放在 URL 中（`/1,2,3`）
- 不可恢复（谨慎使用）

---

### 📊 完整 CRUD 对应关系

| 操作 | HTTP 方法 | 若依注解 | 示例 |
|-----|----------|---------|------|
| **查询列表** | GET | `@GetMapping("/list")` | `GET /system/user/list` |
| **查询详情** | GET | `@GetMapping("/{id}")` | `GET /system/user/1` |
| **新增** | POST | `@PostMapping` | `POST /system/user` |
| **修改** | PUT | `@PutMapping` | `PUT /system/user` |
| **删除** | DELETE | `@DeleteMapping("/{ids}")` | `DELETE /system/user/1,2,3` |
| **导出** | GET | `@GetMapping("/export")` | `GET /system/user/export` |
| **导入** | POST | `@PostMapping("/importData")` | `POST /system/user/importData` |
| **登录** | POST | `@PostMapping("/login")` | `POST /system/login` |
| **退出** | POST | `@PostMapping("/logout")` | `POST /system/logout` |

---

## 🔁 二、后续访问是"一次流程"吗？

### ✅ 是的！后续访问只需 **1 次请求**

###  对比：初次登录 vs 后续访问

#### 初次登录（3 次请求）

```
用户打开浏览器，第一次访问系统：

① POST /login          ← 登录，获取 token
② GET /getInfo         ← 获取用户信息
③ GET /getRouters      ← 获取菜单路由
   ↓
完成！进入主页面
```

**原因**：没有 token，需要登录获取身份凭证。

---

#### 后续访问（1 次请求）

```
用户已经登录，刷新页面或点击菜单：

① GET /system/user/list   ← 直接查询数据
   （自动携带 Authorization: Bearer <token>）
   ↓
完成！直接显示数据
```

**原因**：token 已存在（localStorage），自动携带，无需重新登录。

---

### 🔄 完整流程对比

| 场景 | 请求次数 | 是否携带 Token | 说明 |
|-----|---------|--------------|------|
| **初次登录** | 3 次 | ①不需要，②③需要 | 需要获取 token |
| **刷新页面** | 2 次 | 需要 | token 已存在，直接获取用户信息和菜单 |
| **点击菜单项** | 1 次 | 需要 | 直接查询列表数据 |
| **新增/修改/删除** | 1 次 | 需要 | 直接提交操作 |
| **退出登录** | 1 次 | 需要 | 清除 token |

---

### 💡 生活化类比

#### 初次登录 = 办身份证 + 进门

```
① 去派出所办身份证（POST /login）
   └─ 提交材料，等待审核
   └─ 拿到身份证（token）

② 进门时出示身份证（GET /getInfo）
   └─ 保安核对身份
   └─ 知道你是谁、能进哪些区域

③ 拿大楼地图（GET /getRouters）
   └─ 知道有哪些房间、部门
   └─ 生成导航菜单

--- 完成后，以后每次来：
```

#### 后续访问 = 直接进门

```
① 直接出示身份证（携带 Token 的请求）
   └─ 保安一看：哦，老熟人，进去吧
   └─ 直接去目标房间（查询数据）

不需要再办身份证、不需要再问身份！
```

---

## 🎯 三、Token 自动携带机制

### 前端实现（Axios 拦截器）

```javascript
// request.js - 请求拦截器
axios.interceptors.request.use(
  config => {
    // 从 localStorage 获取 token
    const token = getToken();
    
    if (token) {
      // 自动添加到请求头
      config.headers['Authorization'] = 'Bearer ' + token;
    }
    
    return config;
  },
  error => {
    return Promise.reject(error);
  }
);
```

**效果**：
```javascript
// 你只需要写：
axios.get('/system/user/list');

// 实际发送：
GET /system/user/list HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzUxMiJ9...  ← 自动添加！
```

---

### 后端实现（过滤器）

```java
// JwtAuthenticationTokenFilter.java
@Override
protected void doFilterInternal(HttpServletRequest request, ...) {
    // 从请求头自动提取 token
    String token = request.getHeader("Authorization");  // ← 自动读取
    
    if (token != null && token.startsWith("Bearer ")) {
        token = token.substring(7);  // 去掉 "Bearer "
        
        // 验证 token，获取用户信息
        LoginUser loginUser = tokenService.getLoginUser(token);
        
        // 设置到 Spring Security 上下文
        SecurityContextHolder.getContext().setAuthentication(...);
    }
    
    chain.doFilter(request, response);
}
```

**效果**：
```java
// 业务代码中直接获取当前用户
String username = SecurityUtils.getUsername();  // ← 自动从 token 解析
Long userId = SecurityUtils.getUserId();     // ← 自动从 token 解析
```

---

## 📌 四、总结

### 1️⃣ POST vs GET 使用场景

```
查询数据 → GET（只读，参数在 URL）
新增数据 → POST（创建，数据在 Body）
修改数据 → PUT（更新，数据在 Body）
删除数据 → DELETE（删除，ID 在 URL）
登录/退出 → POST（敏感操作，数据在 Body）
```

### 2️⃣ 后续访问流程

```
初次登录：3 次请求（/login → /getInfo → /getRouters）
后续访问：1 次请求（直接查询，自动携带 token）

原因：token 已存在，前端自动添加 Authorization header
```

### 3️⃣ 核心机制

```
前端：localStorage 存储 token → Axios 拦截器自动添加
后端：JwtAuthenticationTokenFilter 自动解析 token → 设置用户信息
业务代码：SecurityUtils 直接获取当前用户（无需手动传参）
```

---

## ✅ 最终答案

> **后续前端访问后端，每次业务操作只需 1 次请求。**
> 
> - 初次登录：3 次（获取身份 + 用户信息 + 菜单）
> - 后续访问：1 次（直接查询/操作，token 自动携带）
> 
> **GET 用于查询，POST 用于提交/登录。**

这就是若依项目前后端分离架构的核心优势：**一次登录，全程免密**！🎉