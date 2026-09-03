让我搜索一下这个 token 配置在实际代码中是如何使用的，给你一个完整的实例演示。

完美！现在我来给你一个完整的 **token 配置使用实例演示**：

## 🎯 `header: Authorization` 完整使用流程

### 📋 配置说明

在 `application.yml` 中：

```yaml
token:
  header: Authorization      # ← 请求头名称
  secret: abcdefghijklmnopqrstuvwxyz  # ← JWT 签名密钥
  expireTime: 30             # ← 过期时间（分钟）
```

---

## 🔐 完整工作流程演示

### 第一步：用户登录 → 获取 Token

**前端请求**：
```http
POST /login
Content-Type: application/json

{
  "username": "admin",
  "password": "admin123",
  "code": "1234",
  "uuid": "550e8400-e29b-41d4-a716-446655440000"
}
```

**后端处理**（`SysLoginController.java`）：
```java
@PostMapping("/login")
public AjaxResult login(@RequestBody LoginBody loginBody)
{
    AjaxResult ajax = AjaxResult.success();
    // 调用登录服务，验证用户名密码验证码
    String token = loginService.login(
        loginBody.getUsername(), 
        loginBody.getPassword(), 
        loginBody.getCode(),
        loginBody.getUuid()
    );
    // 返回 token 给前端
    ajax.put(Constants.TOKEN, token);  // token 字段名
    return ajax;
}
```

**登录服务**（`SysLoginService.java`）：
```java
public String login(String username, String password, String code, String uuid)
{
    // 1. 验证码校验
    validateCaptcha(username, code, uuid);
    
    // 2. 登录前置校验（密码错误次数等）
    loginPreCheck(username, password);
    
    // 3. 用户验证（Spring Security）
    Authentication authentication = null;
    try
    {
        UsernamePasswordAuthenticationToken authenticationToken = 
            new UsernamePasswordAuthenticationToken(username, password);
        AuthenticationContextHolder.getContext().setAuthentication(authenticationToken);
        authentication = authenticationManager.authenticate(authenticationToken);
    }
    catch (Exception e)
    {
        // 登录失败处理
    }
    
    // 4. 生成 Token
    LoginUser loginUser = (LoginUser) authentication.getPrincipal();
    return tokenService.createToken(loginUser);  // ← 生成 JWT token
}
```

**Token 生成**（`TokenService.java`）：
```java
public String createToken(LoginUser loginUser)
{
    // 1. 生成随机 UUID 作为 token 值
    String token = IdUtils.fastUUID();
    loginUser.setToken(token);
    
    // 2. 设置用户信息到 Redis（缓存登录状态）
    refreshToken(loginUser);
    
    // 3. 创建 JWT claims
    Map<String, Object> claims = new HashMap<>();
    claims.put(Constants.LOGIN_USER_KEY, token);      // 登录用户 key
    claims.put(Constants.JWT_USERNAME, loginUser.getUsername());  // 用户名
    
    // 4. 生成 JWT token（使用 secret 签名）
    return createToken(claims);
}

private String createToken(Map<String, Object> claims)
{
    // 使用 JWT 库生成签名 token
    String token = Jwts.builder()
            .setClaims(claims)
            .signWith(SignatureAlgorithm.HS512, secret)  // ← 使用配置的 secret 签名
            .compact();
    return token;
}
```

**返回结果**：
```json
{
  "code": 200,
  "msg": "操作成功",
  "token": "eyJhbGciOiJIUzUxMiJ9.eyJsb2dpbl91c2VyX2tleSI6IjU1MGU4NDAwLWUyOWItNDFkNC1hNzE2LTQ0NjY1NTQ0MDAwIiwiInVzZXJuYW1lIjoiYWRtaW4ifQ.abc123signature"
}
```

---

### 第二步：前端携带 Token 请求接口

**前端代码**（Axios 拦截器）：
```javascript
// 请求拦截器
axios.interceptors.request.use(config => {
  // 从 localStorage 获取 token
  const token = localStorage.getItem('token');
  
  if (token) {
    // 添加到请求头，使用配置的 header 名称
    config.headers['Authorization'] = 'Bearer ' + token;  // ← TOKEN_PREFIX = "Bearer "
  }
  
  return config;
});

// 调用接口
axios.get('/system/user/list');
```

**实际 HTTP 请求**：
```http
GET /system/user/list HTTP/1.1
Host: localhost:8080
Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJsb2dpbl91c2VyX2tleSI6IjU1MGU4NDAwLWUyOWItNDFkNC1hNzE2LTQ0NjY1NTQ0MDAwIiwiInVzZXJuYW1lIjoiYWRtaW4ifQ.abc123signature
Content-Type: application/json
```

**关键点**：
- `header: Authorization` → 配置在 `application.yml` 中
- `TOKEN_PREFIX = "Bearer "` → 配置在 `Constants.java` 中
- 前端自动拼接：`Authorization: Bearer <token>`

---

### 第三步：后端过滤器验证 Token

**过滤器**（`JwtAuthenticationTokenFilter.java`）：
```java
@Component
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter
{
    @Autowired
    private TokenService tokenService;

    @Override
    protected void doFilterInternal(
        HttpServletRequest request, 
        HttpServletResponse response, 
        FilterChain chain)
        throws ServletException, IOException
    {
        // 1. 从请求头获取 token
        LoginUser loginUser = tokenService.getLoginUser(request);
        
        if (loginUser != null)
        {
            // 2. 验证 token 有效期
            tokenService.verifyToken(loginUser);
            
            // 3. 设置认证信息到 Spring Security 上下文
            Authentication authentication = 
                new UsernamePasswordAuthenticationToken(
                    loginUser, null, loginUser.getPermissions());
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }
        
        // 4. 继续执行后续过滤器链
        chain.doFilter(request, response);
    }
}
```

**获取 Token 方法**（`TokenService.java`）：
```java
private String getToken(HttpServletRequest request)
{
    // 从请求头获取 token（使用配置的 header 名称）
    String token = request.getHeader(header);  // ← header = "Authorization"
    
    if (StringUtils.isNotEmpty(token) && token.startsWith(Constants.TOKEN_PREFIX))
    {
        // 去掉 "Bearer " 前缀
        token = token.substring(Constants.TOKEN_PREFIX.length());
    }
    return token;
}

public LoginUser getLoginUser(HttpServletRequest request)
{
    // 1. 获取 token
    String token = getToken(request);
    
    // 2. 从 Redis 获取登录用户信息
    String userKey = getTokenKey(token);
    LoginUser loginUser = redisCache.getCacheObject(userKey);
    
    return loginUser;
}
```

---

### 第四步：业务代码获取当前用户信息

**Controller 层**（`SysProfileController.java`）：
```java
@RestController
@RequestMapping("/system/user/profile")
public class SysProfileController extends BaseController
{
    /**
     * 获取当前登录用户信息
     */
    @GetMapping("/info")
    public AjaxResult getInfo()
    {
        // 从 Spring Security 上下文获取当前登录用户
        LoginUser loginUser = SecurityUtils.getLoginUser();
        
        SysUser user = loginUser.getUser();
        Set<String> roles = permissionService.getRolePermission(user);
        Set<String> permissions = permissionService.getMenuPermission(user);
        
        return success(user, roles, permissions);
    }
}
```

**工具类**（`SecurityUtils.java`）：
```java
public static class SecurityUtils
{
    /**
     * 获取当前登录用户
     */
    public static LoginUser getLoginUser()
    {
        try
        {
            // 从 SecurityContext 获取认证信息
            return (LoginUser) getAuthentication().getPrincipal();
        }
        catch (Exception e)
        {
            throw new ServiceException("获取用户信息异常", HttpStatus.UNAUTHORIZED);
        }
    }

    /**
     * 获取当前用户名
     */
    public static String getUsername()
    {
        try
        {
            return getLoginUser().getUsername();
        }
        catch (Exception e)
        {
            throw new ServiceException("获取用户账户异常", HttpStatus.UNAUTHORIZED);
        }
    }
}
```

**BaseController 便捷方法**：
```java
public class BaseController
{
    /**
     * 获取当前用户 ID
     */
    protected Long getUserId()
    {
        return SecurityUtils.getUserId();
    }

    /**
     * 获取当前用户名
     */
    protected String getUsername()
    {
        return SecurityUtils.getUsername();
    }

    /**
     * 记录操作日志
     */
    protected void recordOper() throws ServiceException
    {
        SysOperLog log = new SysOperLog();
        log.setModuleName(moduleName);
        log.setOperName(getUsername());  // ← 自动获取当前用户名
        // ... 其他日志信息
    }
}
```

---

## 📊 完整流程图

```
┌─────────────────────────────────────────────────────────────────┐
│                        完整 Token 流程                          │
└─────────────────────────────────────────────────────────────────┘

1️⃣ 用户登录
   ↓
   POST /login {username, password, code}
   ↓
   SysLoginController.login()
   ↓
   SysLoginService.login() → 验证用户名密码
   ↓
   TokenService.createToken() → 生成 JWT
   ↓
   返回 {token: "eyJhbG..."}
   ↓

2️⃣ 前端存储 Token
   ↓
   localStorage.setItem('token', response.token)
   ↓
   Axios 拦截器自动添加：
   Headers: {Authorization: "Bearer eyJhbG..."}
   ↓

3️⃣ 请求受保护接口
   ↓
   GET /system/user/list
   Headers: {Authorization: "Bearer <token>"}
   ↓
   JwtAuthenticationTokenFilter.doFilter()
   ↓
   TokenService.getLoginUser(request)
   ↓
   从请求头获取 token（header = "Authorization"）
   ↓
   从 Redis 获取登录用户信息
   ↓
   设置到 Spring Security Context
   ↓

4️⃣ 业务代码使用
   ↓
   Controller 方法
   ↓
   SecurityUtils.getLoginUser() → 获取当前用户
   ↓
   SecurityUtils.getUsername() → 获取用户名
   ↓
   @PreAuthorize("@ss.hasPermi('system:user:list')") → 权限校验
   ↓
   执行业务逻辑
   ↓

5️⃣ 退出登录
   ↓
   POST /logout
   ↓
   TokenService.delLoginUser(token) → 删除 Redis 缓存
   ↓
   前端清除 localStorage
   ↓
   用户需要重新登录
```

---

## 🎯 关键配置总结

| 配置项 | 值 | 作用 | 使用位置 |
|-------|---|------|---------|
| `token.header` | `Authorization` | 请求头名称 | `TokenService.getToken()` |
| `token.secret` | `abcdefghijklmnopqrstuvwxyz` | JWT 签名密钥 | `TokenService.createToken()` |
| `token.expireTime` | `30` | 过期时间（分钟） | `LoginUser` 缓存过期 |
| `Constants.TOKEN_PREFIX` | `Bearer ` | Token 前缀 | 前端拼接、后端解析 |

---

## 💡 生活化类比

### 机场安检流程

```
1️⃣ 办理登机手续（登录）
   ├─ 出示身份证（用户名密码）
   ├─ 安检验证（验证码）
   └─ 发放登机牌（Token）

2️⃣ 安检口（过滤器）
   ├─ 检查登机牌（Authorization header）
   ├─ 验证登机牌真伪（JWT 签名验证）
   └─ 验证登机牌有效期（过期时间）

3️⃣ 登机（业务代码）
   ├─ 查看登机牌上的姓名（SecurityUtils.getUsername()）
   ├─ 查看登机牌上的座位号（SecurityUtils.getUserId()）
   └─ 前往对应登机口（权限校验）

4️⃣ 下飞机（退出登录）
   └─ 交回登机牌（删除 Redis 缓存）
```

---

## ✅ 总结

**`header: Authorization` 的作用**：

1. **定义请求头名称**：告诉后端从哪个 HTTP 请求头读取 token
2. **统一规范**：前端和后端使用相同的 header 名称
3. **安全性**：token 不放在 URL 或 body 中，放在请求头更安全

**完整使用**：
```yaml
# 配置
token:
  header: Authorization  # 请求头名称
  secret: abcdefg...     # 签名密钥
  expireTime: 30         # 过期时间

# 前端
headers: {Authorization: "Bearer " + token}

# 后端
request.getHeader("Authorization")  # 读取 token
```

这就是若依项目中 token 认证的完整工作流程！🎉