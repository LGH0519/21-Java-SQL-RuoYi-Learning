git clone https://gitee.com/y_project/RuoYi-Vue.git
git clone https://gitee.com/ys-gitee/RuoYi-Vue3.git
太棒了！现在正式开始！我们按顺序一步一步来，每完成一步告诉我结果。

---

## 🚀 第一步：用 IDEA 打开后端项目

1. 打开 IntelliJ IDEA
2. 在欢迎页面点击 **"Open"**（打开）
3. 选择文件夹：`D:\projects\ruoyi-backend`（如果你之前重命名了，就选你改的名字）
4. 点击 **"OK"**
5. IDEA 会自动加载项目，右下角会显示进度条（正在索引和下载 Maven 依赖，第一次会比较慢，可能需要 3-5 分钟）

**完成后告诉我**：IDEA 右下角的进度条消失，项目结构加载完成。

> ⚠️ **注意**：如果 IDEA 提示 "Maven projects need to be imported"，点击 **"Load Maven Project"**。

---

## ⚙️ 第二步：修改数据库配置文件

项目打开后，找到配置文件：

```
ruoyi-backend
├── ruoyi-admin
│   └── src
│       └── main
│           └── resources
│               └── application-druid.yml   ← 找到这个文件
```

1. 双击打开 `application-druid.yml`
2. 找到数据库连接配置部分，修改为你的密码：

```yaml
# 数据源配置
spring:
  datasource:
    druid:
      master:
        url: jdbc:mysql://localhost:3306/ry-vue?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=true&serverTimezone=GMT%2B8
        username: root
        password: root123   # ← 改成你的 MySQL 密码（你设置的是 root123）
```

3. **保存文件**（Ctrl + S）

> 📌 **同时检查**：打开 `application.yml`，确认 Redis 配置是：
> ```yaml
> redis:
>   host: localhost
>   port: 6379
> ```

**完成后告诉我**：配置文件已修改并保存。

---

## 📊 第三步：导入 SQL 数据

1. 打开 **DBeaver**，连接到 `localhost:3306`（root/root123）
2. 在左侧数据库列表中，右键点击 **"Databases"** → **"Create New Database"**
3. 输入数据库名：`ry-vue`，字符集选择 `utf8mb4`，排序规则选择 `utf8mb4_general_ci`
4. 点击 **"OK"** 创建
5. 右键点击新建的 `ry-vue` 数据库 → **"Tools"** → **"Restore database"** → **"Execute Script"**
6. 选择文件：`D:\projects\ruoyi-backend\sql\ry_2021xxxx.sql`（文件名可能略有不同，选那个最大的 `.sql` 文件）
7. 点击 **"Start"** 执行，等待完成
8. 重复步骤 5-7，导入 `quartz.sql` 文件

> 💡 **更简单的方法**：在 DBeaver 中，右键 `ry-vue` 数据库 → **"SQL Editor"** → 把 `.sql` 文件内容拖进去，点击执行按钮（▶️）。

**完成后告诉我**：两个 SQL 文件都执行成功，没有报错。

---

## 🚀 第四步：启动后端

1. 在 IDEA 中，找到启动类：
   ```
   ruoyi-admin
   └── src
       └── main
           └── java
               └── com.ruoyi
                   └── RuoYiApplication.java   ← 找到这个文件
   ```

2. 双击打开 `RuoYiApplication.java`，在 `main` 方法左侧点击绿色三角形 ▶️，选择 **"Run 'RuoYiApplication.main()'"**

3. 观察 IDEA 底部的 **Run** 窗口，等待启动完成（约 20-30 秒）

**成功标志**：看到类似以下信息，说明后端启动成功！
```
(♥◠‿◠) 若依启动成功 ♥
```

**完成后告诉我**：启动成功，或把报错信息截图发我。

---

## 🎨 第五步：安装前端依赖并启动前端

1. 打开 **VS Code**
2. 点击 **"File"** → **"Open Folder"**，选择 `D:\projects\ruoyi-frontend`
3. 打开终端：顶部菜单 **"Terminal"** → **"New Terminal"**
4. 在终端中执行：

```bash
# 安装依赖（第一次会很慢，约 5-10 分钟）
npm install

# 启动前端
npm run dev
```

**成功标志**：终端输出类似：
```
  VITE v4.x.x  ready in xxx ms

  ➜  Local:   http://localhost:80/
  ➜  Network: http://192.168.x.x:80/
```

**完成后告诉我**：启动成功，或把报错信息截图发我。

---

## 🎉 全部完成后

在浏览器访问 `http://localhost`（或 `http://localhost:80`），看到若依登录页面，输入：
- 用户名：`admin`
- 密码：`admin123`

如果能成功登录，恭喜你！**整个若依开发环境搭建完成！** 🎉

---

现在开始第一步：用 IDEA 打开后端项目。完成后告诉我！🚀



